<script>
  import DesignSlider from './components/DesignSlider.svelte';
</script>

<style>
  /* Make sure the max-widths are the same */
  /* for <main> and the design slider  */
  main {
    width: 100%;
    margin: 0 auto;
    max-width: 375px;
    text-align: center;
    background: red;
  }

  @media (min-width: 1000px) {
    main {
      max-width: 1440px;
    }
  }
</style>

<DesignSlider
  sliderPosition="50%"
  width="1440px"
  height="2500px"
  mobileWidth="375px"
  mobileHeight="3000px"
  desktopImg="./images/desktop-design.jpg"
  mobileImg="./images/mobile-design.jpg" />

<main>
  <h1>Design Slider</h1>
  <p>Check the readme for more info.</p>
</main>
