<script>
  import { onMount } from 'svelte';

  // Props
  export let sliderPosition = '50%';
  export let width = '1440px';
  export let height = '3808px';
  export let mobileWidth = '375px';
  export let mobileHeight = '5205px';
  export let desktopImg = 'designSlider/desktop-design.jpg';
  export let mobileImg = 'designSlider/mobile-design.jpg';

  // State
  let dragging = false;
  let measuring = false;
  let measure = 0;
  let measureStart = 0;
  let showMarkup = true;

  // Dragging
  const startDrag = () => {
    dragging = true;
    // Prevent content selection
    document.body.classList.add('prevent-selection');
  };

  const drag = (e) => {
    if (!dragging) return;

    const newPosition = `${e.x - 2 - designSlider.offsetLeft}px`;
    line.style.left = newPosition;
    desktopDesign.style.width = newPosition;
    mobileDesign.style.width = newPosition;

    if (measuring) {
      measure = measureStart + designSlider.offsetLeft - e.x;
    }
  };

  const stopDrag = () => {
    dragging = false;
    // Re-enable selection
    document.body.classList.remove('prevent-selection');
  };

  // Line Positioning
  const setLinePosition = (position) => {
    line.style.left = `${position}px`;
    desktopDesign.style.width = `${position}px`;
    mobileDesign.style.width = `${position}px`;
  };

  const setLineRight = () => {
    const rightLimit = window.innerWidth - designSlider.offsetLeft * 2;
    setLinePosition(rightLimit);
  };

  const setLineLeft = () => {
    setLinePosition(0);
  };

  const moveLine = (e) => {
    if (!(e.key !== 'ArrowLeft' || e.key !== 'ArrowRight')) return;

    const increment = 20;
    const pos = +line.style.left.split('px')[0];
    let newPos = e.key === 'ArrowLeft' ? pos - increment : pos + increment;

    // limit movement to the left
    newPos = newPos < 0 ? 0 : newPos;

    // limit movement to the right
    const rightLimit = window.innerWidth - designSlider.offsetLeft * 2;
    newPos = newPos > rightLimit ? rightLimit : newPos;

    // set positions
    setLinePosition(newPos);
  };

  // Helper to measure horizontal distances
  // Hold down the a key
  const startMeasure = (e) => {
    if (e.key !== 'a') return;
    if (measuring) return;

    measuring = true;
    measureStart = +line.style.left.replace(/px/, '');
  };

  // Release the a key, distance is console.logged
  const stopMeasure = () => {
    if (!measuring) return;
    measuring = false;
    console.log(
      'measure',
      `${Math.abs(measure)}px | ${Math.abs(measure) / 16}rem`
    );
  };

  // Markup and design toggle
  const displayMarkup = (value) => {
    showMarkup = value;

    if (!showMarkup) {
      document.body.classList.add('hide-markup');
    } else {
      document.body.classList.remove('hide-markup');
    }
  };

  const toggleMarkupClass = () => {
    displayMarkup(!showMarkup);
  };

  onMount(async () => {
    // Add Events
    window.addEventListener('mousemove', drag);
    window.addEventListener('mouseup', stopDrag);
    window.addEventListener('mouseleave', stopDrag);
    window.addEventListener('keydown', startMeasure);
    window.addEventListener('keyup', stopMeasure);
    window.addEventListener('keydown', moveLine);

    // Set inital slider position
    const startPos = getComputedStyle(line).left;
    line.style.left = `${startPos}`;
    desktopDesign.style.width = `${startPos}`;
    mobileDesign.style.width = `${startPos}`;
  });
</script>

<style>
  #designSlider {
    margin: 0 auto;
    max-width: var(--width);
    width: 100%;
    position: relative;
    border-top: none;
    padding: 0;
  }

  .background-container {
    position: absolute;
    left: 0;
    width: var(--sliderPosition);
    z-index: 2;
    z-index: -1000;
  }

  img {
    width: 100%;
    height: 100%;
    object-fit: none;
    object-position: top left;
  }

  #desktopDesign {
    height: var(--height);
  }

  #mobileDesign {
    height: var(--mobile-height);
    display: none;
  }

  #line {
    position: absolute;
    height: var(--height);
    width: 4px;
    background: orange;
    z-index: 3;
    left: var(--sliderPosition);
    cursor: grabbing;
  }

  @media (max-width: 1000px) {
    #designSlider {
      max-width: var(--mobile-width);
    }
    #desktopDesign {
      display: none;
    }

    #mobileDesign {
      display: unset;
    }

    #line {
      height: var(--mobile-height);
    }
  }

  /* Prevent user selection while dragging */
  /* This class gets applied to the body  */
  :global(.prevent-selection) {
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }

  /* Helper class which will hide all sections. */
  /* Useful if you want to check just the design image */
  /* Used with the "Hide Markup" button */
  :global(.hide-markup section) {
    display: none;
  }

  nav {
    position: fixed;
    top: 0;
    right: 0;
    opacity: 0.2;
    z-index: 1000;
  }

  nav:hover {
    opacity: 1;
  }

  button {
    display: block;
    margin-bottom: 0.5rem;
  }
</style>

<div id="designSlider" style="--width: {width}; --mobile-width: {mobileWidth};">
  <nav>
    <button on:click={() => toggleMarkupClass()}>Toggle Markup </button>

    <button
      on:click={() => {
        setLineLeft();
        displayMarkup(true);
      }}>Markup Only</button>
    <button
      on:click={() => {
        displayMarkup(false);
        setLineRight();
      }}>Design Only</button>

    <button
      on:click={() => {
        setLinePosition(200);
        displayMarkup(true);
      }}>Show All
    </button>
  </nav>

  <div
    id="desktopDesign"
    class="background-container"
    style="
        --height: {height};
        --width: {width};
        --sliderPosition: {sliderPosition};
    ">
    <img src={desktopImg} alt="desktop design" />
  </div>

  <div
    id="mobileDesign"
    class="background-container"
    style="
        --mobile-height: {mobileHeight};
        --sliderPosition: {sliderPosition};
    ">
    <img src={mobileImg} alt="mobile design" />
  </div>

  <div
    id="line"
    on:mousedown={startDrag}
    style="
      --height: {height};
      --mobile-height: {mobileHeight};
      --sliderPosition: {sliderPosition};
      " />
</div>
