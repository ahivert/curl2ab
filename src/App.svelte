<script>
  let abInput = "";
  let concurrency = 1;
  let copyStr = "Copy";
  let curlInput = "";
  let curlPlaceholder = `curl '${window.location}'`
  let errors = {};
  let iteration = 10;
  const headersToCopy = [
    "origin",
    "authorization",
    "accept",
    "cookie",
    "user-agent",
    "accept-encoding",
    "accept-language",
    "upgrade-insecure-requests",
    "x-api-token"
  ];

  function curl2ab() {
    errors.curl = null;
    const curlElments = curlInput.split(/\s+'|'\s*\\?\s*/);
    const url = curlElments[1];
    if (curlInput && curlInput.indexOf("curl") === 0 && url) {
      curlElments.shift()
      let abString = `ab -n ${iteration} -c ${concurrency}`;
      curlElments.forEach((element, index) => {
        if (element === "-H") {
          abString += ` -H '${curlElments[index+1]}'`;
        }
      });
      abInput = `${abString} '${url}'`;
    } else if (curlInpu.length >= 4) {
      errors.curl = `cURL command must start with <code>curl</code> and followed by the url wrapped in single quote like <code>'${document.location}'</code>`;
    }
  }

  function copy() {
    if (abInput.length > 0) {
      let el = document.querySelector("#ab");
      el.select();
      document.execCommand("copy");
      const prevStr = copyStr;
      copyStr = "Copied!";
      setTimeout(() => (copyStr = prevStr), 3000);
    }
  }
</script>

<form action="">
  <label for="curl">Paste your cURL command</label>
  <textarea
    name="curl"
    id="curl"
    bind:value={curlInput}
    on:input={curl2ab}
    placeholder={curlPlaceholder} />
  {#if errors.curl}
    <small class="error">
      {@html errors.curl}
    </small>
  {/if}
  <div class="flex">
    <div>
      <label for="iteration">Iteration</label>
      <input
        type="number"
        name="iteration"
        id="iteration"
        step="10"
        bind:value={iteration}
        on:input={curl2ab} />
    </div>
    <div>
      <label for="concurrency">Concurrency</label>
      <input
        type="number"
        name="concurrency"
        id="concurrency"
        step="1"
        bind:value={concurrency}
        on:input={curl2ab} />
    </div>
  </div>
</form>
<hr />
<div class="result">
  {#if abInput && curlInput}
    <h4>ab command</h4>
    <button class="copy" on:click={copy}>{copyStr}</button>
    <pre>{abInput}</pre>
    <input class="hidden" id="ab" value={abInput} />
  {/if}
</div>
