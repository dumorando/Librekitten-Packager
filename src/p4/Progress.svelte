<script>
  export let progress = 0;
  export let text = '';
</script>

<style>
  .progress {
    display: flex;
    align-items: center;
    flex-direction: column;
  }
  .bar-outer {
    width: 200px;
    height: 10px;
    border: 1px solid currentColor;
    margin-bottom: 4px;
    background: black;
    border-radius:5px;
  }
  :global([theme="dark"]) .bar-outer {
    background: transparent;
  }
  .bar-inner {
    height: 100%;
    width: 0;
    background-image: linear-gradient(
      45deg,
      #353535 25%,
      transparent 25%,
      transparent 50%,
      #353535 50%,
      #353535 75%,
      transparent 75%,
      transparent
    );
  }
  .text {
    font-style: italic;
  }
</style>

<div class="progress">
  <div class="bar-outer">
    <div
      class="bar-inner"
      style:width={`${progress * 100}%`}
    ></div>
  </div>
  <div class="text">
    {text}
  </div>
</div>
