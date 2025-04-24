# infinite-craft-bot


Copy paste the code to the console of the page.
To open console do ctrl+shift+I and then hit console. 
Its fast and auto cleans. 


Its not anyting else, check the whole code before pasting it. 

# CODE:
```(async function () {
  const log = (...args) => console.log("[🖱️ Infinite Craft Bot]", ...args);
  const delay = ms => new Promise(res => setTimeout(res, ms));
  const getRandomItem = arr => arr[Math.floor(Math.random() * arr.length)];

  let latestCrafted = null;

  // Updated coordinates for 5 crafting locations that are spread out
  const craftingCoordinates = [
    { x: 200, y: 200 },
    { x: 500, y: 200 },
    { x: 800, y: 200 },
    { x: 500, y: 400 },
    { x: 800, y: 400 }
  ];

  // Get item elements from the DOM
  function getElementNodes() {
    return Array.from(document.querySelectorAll('[data-item-text]'));
  }

  // Simulate mouse drag-and-drop operation
  function simulateMouseDrag(el, toX, toY) {
    const rect = el.getBoundingClientRect();
    const fromX = rect.left + rect.width / 2;
    const fromY = rect.top + rect.height / 2;

    const simulateEvent = (type, x, y) => {
      const evt = new MouseEvent(type, {
        bubbles: true,
        clientX: x,
        clientY: y,
      });
      el.dispatchEvent(evt);
    };

    simulateEvent('mousedown', fromX, fromY);
    simulateEvent('mousemove', toX, toY);
    simulateEvent('mouseup', toX, toY);
  }

  // Function to clear the crafting area by clicking the clear button and confirming the action
  async function clickClearButton() {
    const clearButton = document.querySelector('.clear.tool-icon');
    if (clearButton) {
      clearButton.click();
      log("Clicked the clear button!");

      // Wait for the confirmation popup and click "Yes"
      const yesButton = document.querySelector('.action-btn.action-danger');
      if (yesButton) {
        await delay(500); // Wait a bit for the popup to appear
        yesButton.click();
        log("Clicked 'Yes' on the confirmation popup!");
      } else {
        log("Yes button not found.");
      }
    } else {
      log("Clear button not found.");
    }
  }

  // Craft items at specified coordinates
  async function craftItems(el1, el2, x, y) {
    simulateMouseDrag(el1, x, y);
    await delay(500); // Slight delay before dropping the second item
    simulateMouseDrag(el2, x + 10, y + 10); // Slight offset for the second item
    await delay(1000); // Wait for the crafting process to complete (1 second)
  }

  // Main bot loop
  async function botLoop() {
    while (true) {
      const elements = getElementNodes();
      if (elements.length < 2) {
        log("Not enough items yet.");
        await delay(1000);
        continue;
      }

      // Pick random coordinates for crafting (5 coordinates spaced apart)
      const coordinates = [...craftingCoordinates];
      const randomCoordinates = [];

      while (randomCoordinates.length < 5) {
        const randomIndex = Math.floor(Math.random() * coordinates.length);
        const coord = coordinates.splice(randomIndex, 1)[0];
        randomCoordinates.push(coord);
      }

      // Craft items at each of the random coordinates
      const craftingPromises = randomCoordinates.map(async (coord) => {
        let el1, el2;

        // Select two random items for each coordinate
        el1 = getRandomItem(elements);
        el2 = getRandomItem(elements.filter(item => item !== el1));  // Ensure el2 is different from el1

        log(`Combining: ${el1.dataset.itemText} + ${el2.dataset.itemText} at coordinates (${coord.x}, ${coord.y})`);

        // Craft the items at the given coordinates
        await craftItems(el1, el2, coord.x, coord.y);
      });

      // Wait for all crafting processes to finish
      await Promise.all(craftingPromises);

      // Update the latest crafted item (last element in the list)
      latestCrafted = elements[elements.length - 1];  // Get the last element in the list
    }
  }

  // Function to periodically click the clear button every 30 seconds
  async function periodicClearButton() {
    while (true) {
      await delay(30000); // Wait 30 seconds
      await clickClearButton(); // Click the clear button and confirm
    }
  }

  // Start the bot and the periodic clear button click
  botLoop();
  periodicClearButton();
})();
```
