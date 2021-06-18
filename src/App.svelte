<script>
  import { Howl } from 'howler';
	import Tasks from './Tasks.svelte';

  const music = new Howl({
    src: ['pikmin.mp3'],
    loop: true,
    volume: 0.2
  });

  let mode = null;

  const tasks = {
    am: [
      { name: "Make Bed", time: 5, img: "tidy" },
      { name: "Brush Your Teeth", time: 3, img: "brush" },
      { name: "Potty Break", time: 2, img: "poop" },
    ],
    pm: [
      { name: "Tidy Up", time: 5, img: "tidy" },
      { name: "Brush Your Teeth", time: 3, img: "brush" },
      { name: "Potty Break", time: 2, img: "poop" },
    ]
  }

  // Grab time
  const date = new Date();
  const hr = date.getHours();
  let time = hr < 12 ? 'am' : 'pm';
  let canStart = true || (hr >= 8 && hr <= 10) || (hr >= 7 && hr <= 9);
  let interval;

  const setMode = (m) => {
    mode = m;
    checkMode();
  }

  function checkMode() {
    if (mode === 'routine') {
      music.play();
      setTimer(300);
    } else {
      music.stop();
      timer = 0;
      clearInterval(interval);
    }
  }

  // Timer code
  let timer;

  function setTimer(n = 11) {
    if (interval) clearInterval(interval);
    timer = n;

    interval = setInterval(function() {
      timer -= 1;
      if (timer === 0) clearInterval(interval);
    }, 1000);
  }

  function stopTimer() {
    clearInterval(interval);
    timer = -1;
  }
</script>


<div class="container" data-mode={mode}>
  <section class="main">
    <img src="e.png" alt="Captain E" class="eman animate__animated animate__wobble animate__infinite animate__slower">

    <button class="start orb" disabled={!canStart} on:click={() => { setMode('routine')} }>
      {time === 'pm' ? '🌘' : '☀️'}
    </button>
    <!-- <button class="orb" on:click={() => { setMode('score')} }>Score</button> -->
  </section>

  <section class="routine" data-time={time}>
    <Tasks 
      tasks={tasks[time]} 
      timer={timer} 
      time={time}
      mode={mode}
      setTimer={setTimer} 
      stopTimer={stopTimer}
      setMode={setMode}
    /> 
    <button on:click={() => { setMode(null)} }>Home</button>
  </section>

  <section class="score">
    <button on:click={() => { setMode(null)} }>Home</button>
  </section>
</div>

<style>
  .orb {
    font-size: 5em;
  }

  section:not(.main) .orb {
    position: absolute;
    top: 10px; right: 10px;
  }

  .start {
    background: transparent;
    border: 0 none;
    position: absolute;
    bottom: 20px; left: 50%;
    transform: translateX(-50%);
  }
  .start:active {
      transform: translateX(-50%) scale(0.9);
    }

  .eman {
    display: block;
    filter:drop-shadow(1px 1px 5px rgba(0, 0, 0, 0.2));
    margin: 15vh auto;
    max-width: 80%;
  }
</style>