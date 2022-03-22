<script>
    import { goto } from "$app/navigation";

    import axios from "axios";

    export let query;
    export let response;

    let inputElement;
    let typingTimer;

    function handleKeyup() {
        clearTimeout(typingTimer);
        typingTimer = setTimeout(doneTyping, 100);
    }

    function handleKeydown(ev) {
        if (ev.key == "Enter") {
            doneTyping();
        }

        clearInterval(typingTimer);
    }

    function sanitizeQuery(query) {
        return query
                .replaceAll(" ", "_")
                .trim()
    }

    function doneTyping() {
        let sanitizedQuery = sanitizeQuery(query);

        axios.get(`https://bible-api.com/${sanitizedQuery}`)
             .then((res) => {
                response = res.data;
                goto(`/?query=${sanitizedQuery}`, { keepfocus: true })
             })
             .catch((err) => {
                 response = {
                     reference: "404",
                     verses: [
                        {text: "Page not found", verse: "404"},
                     ]
                 }
             });
    }

    if (query) {
        doneTyping();
    }
</script>

<div class="flex h-12 items-center">
  <svg width="20" height="20" viewBox="0 0 20 20">
    <path
      fill="none"
      fill-rule="evenodd"
      stroke="currentColor"
      stroke-linecap="round"
      stroke-linejoin="round"
      d="M14.386 14.386l4.0877 4.0877-4.0877-4.0877c-2.9418 2.9419-7.7115 2.9419-10.6533 0-2.9419-2.9418-2.9419-7.7115 0-10.6533 2.9418-2.9419 7.7115-2.9419 10.6533 0 2.9419 2.9418 2.9419 7.7115 0 10.6533z"
    />
  </svg>
  <input 
        class="w-full ml-2 outline-none" 
        type="text" 
        placeholder="ex. John 1" 
        bind:this={inputElement}
        bind:value={query} 
        on:keyup={handleKeyup}
        on:keydown={handleKeydown}
    />
</div>

<style>
</style>
