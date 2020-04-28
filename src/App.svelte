<script>
  let iteration = 10;
  let concurrency = 1;
  let curl = "";
  let ab = "";
  let errors = {};
  let copyStr = "Copy";

  function curl2ab() {
    errors.curl = null;
    if (curl && curl.indexOf("curl") === 0) {
      const curlElments = curl.split(" ");
      const url = curlElments[1];
      let baseString = `ab -n ${iteration} -c ${concurrency}`;
      let cookieString = "";
      if (curl.indexOf("Cookie") > 0) {
        cookieString = `-C '${curl.match(/-H 'Cookie: ([^']*|$)/)[1]}'`;
      }

      ab = cookieString
        ? `${baseString} ${cookieString} ${url}`
        : `${baseString} ${url}`;
    } else if (curl.length >= 4) {
      errors.curl = "cURL command must start with <code>curl</code>";
    }
  }

  function copy() {
    if (ab.length > 0) {
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
  <label for="curl">Your cURL command</label>
  <textarea
    name="curl"
    id="curl"
    bind:value={curl}
    on:input={curl2ab}
    placeholder="curl https://google.com" />
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
        step="1"
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
  {#if ab && curl}
    <h4>ab command</h4>
    <button class="copy" on:click={copy}>{copyStr}</button>
    <pre>{ab}</pre>
    <input class="hidden" id="ab" value={ab} />
  {/if}
</div>
