<script>
  import cartas from "./cardsContent.json";
  import { onMount } from "svelte";
  import {  cardSizeBase, cardSizeHover, cardSizeClicked, contentBackgBase, contentBackgHover, contentBackgClicked, updateWindowSize } from "./cardSizes.js"

  let numCards = cartas.data;
  console.log("numCards: ", numCards);


  // Add the resize event listener when the component is mounted
  onMount(() => {
    // Initial window size update
    updateWindowSize();

    // Add the event listener for window resize
    window.addEventListener("resize", updateWindowSize);

    // Clean up the event listener when the component is destroyed
    return () => {
      window.removeEventListener("resize", updateWindowSize);
    };
  });

  let clickedCardIndex = null;

  function handleCardClick(index) {
    if (clickedCardIndex === index) {
      // If the clicked card is already expanded, set clickedCardIndex back to null
      clickedCardIndex = null;
    } else {
      // Otherwise, set clickedCardIndex to the index of the clicked card
      clickedCardIndex = index;
    }
  }

  function handleCardKeyDown(event, index) {
    if (event.key === "Enter" || event.key === " ") {
      // Trigger the same action as a click when Enter or Space is pressed
      handleCardClick(index);
    }
  }

  function handleScroll(event) {
    const cardsContainer = document.querySelector(".cards-wrapper");
    cardsContainer.scrollLeft += event.deltaY; // Horizontal scrolling based on Y-axis mouse wheel
    event.preventDefault();
  }

  let hoveredCardIndex = null;

  function handleCardMouseEnter(index) {
    hoveredCardIndex = index;
  }

  function handleCardMouseLeave(index) {
    if (clickedCardIndex !== index) {
      hoveredCardIndex = null;
    }
  }


  let touchStartX = 0;
let touchStartY = 0;
let touchEndX = 0;
let touchEndY = 0;

function handleTouchStart(event) {
  const touch = event.touches[0];
  touchStartX = touch.clientX;
  touchStartY = touch.clientY;
}

function handleTouchMove(event) {
  event.preventDefault();
  const touch = event.touches[0];
  touchEndX = touch.clientX;
  touchEndY = touch.clientY;
}

function handleTouchEnd() {
  const deltaX = touchEndX - touchStartX;
  const deltaY = touchEndY - touchStartY;
  const threshold = 50; // Adjust this threshold value as needed

  // Check if the touch movement is more horizontal or vertical
  if (Math.abs(deltaX) > Math.abs(deltaY)) {
    // Horizontal scroll
    const cardsContainer = document.querySelector(".cards-wrapper");
    cardsContainer.scrollLeft += deltaX;

  
  } else {
    // Vertical scroll (not needed for your current implementation)
  }
}


</script>
<div class="portBkg">
<div class="cards-viewport" on:wheel={handleScroll} on:touchstart={handleTouchStart} on:touchmove={handleTouchMove} on:touchend={handleTouchEnd}
on:touchstart={handleTouchStart}
on:touchmove={handleTouchMove}
on:touchend={handleTouchEnd}>
  <div class="cards-wrapper">
    {#each [...numCards, ...numCards, ...numCards] as card, index}
      <div
        class="cards-container"
        role="button"
        tabindex="0"
        on:click={() => handleCardClick(index)}
        on:mouseenter={() => handleCardMouseEnter(index)}
        on:mouseleave={() => handleCardMouseLeave(index)}
        on:keydown={(event) => handleCardKeyDown(event, index)}
        style={`${
          clickedCardIndex === index
            ? `${cardSizeClicked}`
            : hoveredCardIndex === index
            ? `${cardSizeHover}`
            : `${cardSizeBase}`
        }`}
      >
        <div
          class="card"
          style={`background-image: url(${card.Image});
          background-position: center;
          background-size: cover;
            ${
              clickedCardIndex === index
                ? `${cardSizeClicked}`
                : hoveredCardIndex === index
                ? `${cardSizeHover}`
                : `${cardSizeBase}`
            }`}
        >
          <div class="content-container"
          role="button"
          tabindex="0"
          on:click={() => handleCardClick(index)}
          on:mouseenter={() => handleCardMouseEnter(index)}
          on:mouseleave={() => handleCardMouseLeave(index)}
          on:keydown={(event) => handleCardKeyDown(event, index)}
          style={`${
            clickedCardIndex === index
              ? `${contentBackgClicked}`
              : hoveredCardIndex === index
              ? `${contentBackgHover}`
              : `${contentBackgBase}`
          }`}>
            <a href="{card.Link}" target="_blank">
              <h2>{card.Title}</h2>
            </a>
              <h3>{card.Content}</h3>
              <p class={clickedCardIndex === index ? "expanded" : ""}>
                {card.About}
              </p>
          </div>
        </div>
      </div>
    {/each}
  </div>
</div>
</div>

<style>

.portBkg{
    background-color: rgb(31, 31, 31);
    margin: 0;
    position: relative;
    width: 100vw;
}


a{
  position: relative;
  z-index: 50000;
  display: flex;
  width: 20vw;
  text-decoration: none;
    cursor: pointer;
}

a:hover{
  text-decoration: underline;
}

  .cards-viewport {
    overflow: hidden;
    position: relative;
    display: flex;
    justify-content: flex-start;
    align-items: center;
    height: 100vh;
    margin: 0 10vh;
  }

  .cards-wrapper {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    width: 90vw;
    height: 100vh;
    border-radius: 8px;
    /* Removed height: 80vh; */
    /* Removed scrollbar-width: none; */
    padding-left: 3rem;
    padding-right: 3rem;
    gap: 1rem;
    overflow-x: hidden; /* Enable horizontal scrolling */
    overflow-y: hidden; /* Hide vertical scrollbar */
  }


  .cards-container {
    display: flex;
    transition: width 2s ease, height 2s ease; /* Add transition for width and height changes */
    overflow-x: visible;
    /* Removed transition: transform 0.2s ease; */
  }

  .card {
    width: 30vw;
    background-color: rgba(0, 0, 0, 0.1);
    background-size: cover;
    border-radius: 35px;
    margin: 0;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
    display: flex;
    overflow: hidden;
    flex-direction: column;
    justify-content: flex-end;
    transition: width 2s ease, height 2s ease; /* Add transition for width and height changes */
    position: relative; /* Add position relative to the .card element */
  }

  /* Add the pseudo-element to create the background overlay */
  .card::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0.5; /* Set initial opacity to 0 */
    background-color: rgba(
      0,
      0,
      0,
      0.3
    ); /* Adjust the background color on hover as needed */
    transition: opacity 0.5s ease; /* Add transition for opacity changes */
  }

  .card:active::before {
    opacity: 1; /* Adjust the opacity value when active (clicked) as needed */
  }

  .content-container {
    max-height: 25%;
    position: relative;
    padding: 1rem 0;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: left;
    flex: 1;
    flex-wrap: nowrap;
  }


  .content-container h2 {
    margin:0 24px;

  }

  .content-container h3 {
    margin-left:24px;

  }

  .content-container p {
    margin:0 24px;
    visibility: hidden;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    max-height: 6em;
    opacity: 0;
    transition: max-height 2s ease, opacity 2s ease;
    position: relative;
  }

  .content-container p:hover {
    text-overflow: ellipsis;
    overflow-wrap: break-word;
    max-height: 6em;
    transition: max-height 2s ease, opacity 2s ease;
  }

  .content-container p.expanded {
    visibility: visible;
    height: auto;
    max-height: 18em;
    width: 100%;
    opacity: 1;
    transition: max-height 2s ease, opacity 2s ease;
    white-space: normal;
    position: relative;
  }

  .content-container:hover p:not(.expanded) {
    visibility: visible;
    height: 6em;
    width: 100%;
    opacity: 1;
    transition: max-height 2s ease, opacity 2s ease;
  }

  /* Responsive styles for mobile */
  @media (max-width: 600px) {
    .cards-viewport {
      width: 90vw;
      height: 60vh; /* Adjust height as needed */
      flex-direction: column; /* Stack cards vertically */
      justify-content: flex-start;
      align-items: center;
    }

    .cards-wrapper {
      /* Adjust padding and gap for mobile */
      padding: 0;
      gap: 0.5rem;
      align-items: center;
    }

    .cards-container {
      /* Adjust flex and width for mobile */
      flex: 0 0 auto;
      width: 70vw; /* Adjust card width for mobile */
      align-items: center;
      flex-wrap: nowrap;
    }

    .content-container p {
      /* Adjust max-height for mobile */
      max-height: 4em; /* Adjust max-height for mobile */
    }

    .content-container:hover p:hover {
      /* Adjust max-height for mobile */
      max-height: 4em; /* Adjust max-height for mobile */
    }

    .content-container p.expanded {
      /* Adjust max-height for mobile */
      max-height: 12em; /* Adjust max-height for mobile */
    }
  }
</style>