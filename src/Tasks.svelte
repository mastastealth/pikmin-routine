<script>
  import { Howl } from 'howler';

  export let tasks = {};
  export let timer;
  export let time;
  export let mode;
  export let setTimer = () => {};
  export let stopTimer = () => {};
  export let setMode = () => {};

  let currentTask = 0;
  let showYay = false;
  let showOof = false;

  const sfx_yay = new Howl({
    src: ['yay.mp3'],
    volume: 0.5
  });

  const sfx_oof = new Howl({
    src: ['dead.mp3'],
    volume: 0.5
  });

  function showCongrats() {
    sfx_yay.play();
    showYay = true;
    stopTimer();
  }

  function showFail() {
    sfx_oof.play();
    showOof = true;
    stopTimer();
  }

  function nextTask() {
    currentTask += 1;
    showYay = false;
    showOof = false;
    if (currentTask < tasks.length) setTimer(tasks[currentTask].time * 60);
  }

  function finishTasks() {
    showYay = false;
    showOof = false;
    setMode(null);
    setTimeout(() => {
      currentTask = 0;
    }, 1500);
  }

  $: {
    console.log(timer);
    if (timer === 0 && currentTask < tasks.length && mode === "routine") {
      showFail();
    }
  }
</script>

<section class="tasks" data-time={time}>
  {#each tasks as task, i}
    <article class="task" data-current={currentTask === i} data-upcoming={i > currentTask}>
      <header>
        <h2>{task.name}</h2>
        <div class="timer">
          <div class="bar" style="transform: scaleX({timer /(task.time * 60)})"></div>
        </div>
      </header>

      <div class="img animate__animated animate__pulse animate__infinite animate__slower">
        <img src={`${task.img}.png`} alt={task.name} />
      </div>

      <footer>
        <button on:click={showCongrats}>Done!</button>
      </footer>
    </article>
  {/each}

  <article class="task" data-current={currentTask === tasks.length} data-upcoming={currentTask < tasks.length}>
    <header>
      <h2>All Done</h2>
    </header>

    <div class="img animate__animated animate__pulse animate__infinite animate__slower">
      {#if time === "am"}
        <img src="pencil.png" alt="All done." />
      {:else}
        <img src="sleep.png" alt="All done." />
      {/if}
    </div>
  
    <footer>
      <button on:click={finishTasks}>
        {time === "am" ? "Have Fun!" : "Good Night!"}
      </button>
    </footer>
  </article>
</section>

<div class="yay" data-show={showYay ? true : null}>
  <img class="animate__animated animate__tada animate__infinite" src="yay.png" alt="Yay!">

  <button on:click={nextTask}>Next</button>
</div>

<div class="oof" data-show={showOof ? true : null}>
  <img src="oof.png" alt="oof!">

  <button on:click={nextTask}>Next</button>
</div>

<div class="overlay"></div>

<style>
  .timer {
    background: grey;
    border: 2px solid black;
    border-radius: 5px;
    height: 50px;
    position: relative;
  }
    .timer:before {
      background: white;
      border-radius: 8px;
      content: '';
      height: 10px;
      opacity: 0.5;
      position: absolute;
      left: 10px; top: 5px;
      width: calc(100% - 20px);
      z-index: 1;
    }

  .bar {
    background: linear-gradient(to bottom, yellow, goldenrod);
    height: 100%;
    transform-origin: left center;
  }

  .tasks {
    color: white;
    height: 100%;
  }
    .tasks[data-time="am"] { background: skyblue; }
    .tasks[data-time="pm"] { background: linear-gradient(to bottom, rgb(38, 28, 73), rgb(25, 2, 54)); }

  .task {
    display: flex;
    flex-direction: column;
    height: 100%;
    padding: 10px;
    position: absolute;
    top: 0; left: 0;
    text-align: center;
    transition: transform 0.5s;
    transform: translateX(-100%);
    width: 100%;
  }
    .task[data-current="true"] { transform: translateX(0); }
    .task[data-upcoming="true"] { transform: translateX(100%); }

    .task h2 {
      margin-bottom: 5px;
    }

    .task img {
      display: block;
      max-height: 50vh;
      max-width: 100%;
    }

    .task button {
      background: linear-gradient(to bottom, green, rgb(0, 98, 49));;
      border: 2px solid darkgreen;
      color: white;
      font-size: 2em;
      font-weight: bold;
      padding: 10px 30px;
    }

  .img {
    display: grid;
    flex: 1;
    margin: 0 auto;
    place-content: center;
  }

  .overlay {
    background: black;
    height: 100%;
    opacity: 0;
    pointer-events: none;
    position: fixed;
    top: 0; left: 0;
    transition: opacity 0.3s;
    width: 100%;
    z-index: 1;
  }
    [data-show] ~ .overlay {
      opacity: 0.75;
      pointer-events: auto;
    }

  .yay, .oof {
    background: white;
    max-height: 90vh;
    padding: 10px;
    position: fixed;
    top: 50%; left: 50%;
    text-align: center;
    transition: transform 0.5s;
    transform: translate(-50%, -50%);
    width: 95%;
    z-index: 2;
  }
    .yay:not([data-show]),
    .oof:not([data-show]) {
      transform: translate(-50%, 200%);
    }

    .yay img,
    .oof img {
      max-height: 50vh;
      max-width: 100%;
    }

    .yay button,
    .oof button {
      background: linear-gradient(to bottom, green, rgb(0, 98, 49));
      border: 2px solid darkgreen;
      color: white;
      display: block;
      font-size: 1.5em;
      font-weight: bold;
      padding: 10px 30px;
      width: 100%;
    }

    .oof button {
      background: linear-gradient(to bottom, red, rgb(168, 53, 0));
      border-color: darkred;
    }

</style>