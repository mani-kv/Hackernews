<script>
  import {onMount} from 'svelte'
  import './assets/style/global.css'
  import Skeleton from './lib/Skeleton.svelte'
  let news = []
  let newBatch = []
  let from = 0
  let to = 15

  // let placeholderText = 'Loading Data....'

  onMount(async () => {
    try{

      const res = await fetch(`https://hacker-news.firebaseio.com/v0/topstories.json`);
      let id = await res.json();
      id.forEach((item)=> {
        news.push({'id': item, 'post': ''})
      })
    }catch{
      // placeholderText = 'Error fetching data'
    }

    // now load data for each id in batches of 25
    newBatch = news.slice(from,to)
    fetchNewBatch(newBatch)
    setTimeout(() => {
      // newBatch = newBatch.concat(tmpBatch)
      newBatch = newBatch
    }, 1000);
    
	});


  function fetchNewBatch (postArr) {
    try{
      postArr.forEach(async (ITEM)=> {
        const res = await fetch(`https://hacker-news.firebaseio.com/v0/item/${ITEM.id}.json`);
        let post = await res.json();
        ITEM.post = post
      })
      // console.log(newBatch[from], newBatch[from+1], newBatch[from+2])
    } catch {
      // placeholderText = 'Error fetching data'
    }
  }

  let scrollY
  $:  percentScroll = Math.round(scrollY / (document.body.offsetHeight - window.innerHeight) *100)
  // $: console.log(percentScroll)

  $: if(percentScroll > 60) {
    from = from *2
    to = to *2
    newBatch = news.slice(from,to)
    fetchNewBatch(newBatch)
    setTimeout(() => {
      // newBatch = newBatch.concat(tmpBatch)
      newBatch = newBatch
    }, 2000);
  }

</script>
<svelte:window bind:scrollY={scrollY} />
<main class="p-8 bg-gradient-to-r from-indigo-700 via-purple-600 to-pink-500 min-h-screen text-center sm:p-20" >
  <div class="flex flex-col"  >


    <span class="text-4xl font-semibold bg-clip-text text-transparent bg-white tracking-wider mt-20 mb-20">HackerNews</span>
  
    <div class=" text-left inline-block align-center max-w-screen-md self-center "  >

      
      
      
      {#each  newBatch as posts}
        {#if posts.post.title}
          <a href={posts.post.url} target="_blank" class="mb-8 rounded-md bg-white flex flex-row shadow-sm"  >
            <div class="w-24 p-6 inline-block text-center self-center">
              <span class="text-xl font-bold text-blue-700 sm:text-2xl ">{posts.post.score }</span>

            </div>
            <div class="flex flex-col w-auto p-6">
              
              <span class="text-md text-stone-800 mb-4 sm:text-lg "> {posts.post.title } </span>
              <span class="text-xs text-stone-600 mb-2 sm:text-sm ">{new Date(posts.post.time * 1000).toLocaleDateString("en-US", { year: 'numeric', month: 'long', day: 'numeric', timeZone: 'UTC' }) + ' - ' + new Date(posts.post.time * 1000).toLocaleTimeString("en-US", {hour: '2-digit', minute: '2-digit'}) }</span>
            </div>
          </a>
          
          {:else}
          
          <!-- <span class="text-lg text-white flex"> {placeholderText} </span> -->
          <Skeleton />
          
          {/if}
        {/each}
    </div>
  </div>
  
</main>
