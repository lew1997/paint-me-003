<!-- Include p5.js library -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.6.0/p5.js"></script>

<!-- Custom JavaScript -->
<script>
  document.addEventListener("DOMContentLoaded", function () {
    let img;
    let currentSizes = [];
    let targetSizes = [];
    const tileCount = 120; // Number of tiles in the grid
    let tileSize;
    let imgAspectRatio;

    function preload() {
      // Load the image
      img = loadImage(
        "https://raw.githubusercontent.com/lew1997/Webflow-paint-me-/653cdac05e24a7852088d0f7c2e59a4644d911da/me%205.png"
      );
    }

    function setup() {
      createCanvas(windowWidth, windowHeight, WEBGL);

      // Calculate the image aspect ratio
      imgAspectRatio = img.width / img.height;

      // Resize the image to fit the canvas while preserving its aspect ratio
      if (width / height > imgAspectRatio) {
        img.resize(height * imgAspectRatio, height);
      } else {
        img.resize(width, width / imgAspectRatio);
      }

      // Initialize the grids
      for (let x = 0; x < tileCount; x++) {
        currentSizes[x] = [];
        targetSizes[x] = [];
        for (let y = 0; y < tileCount; y++) {
          currentSizes[x][y] = 0;
          targetSizes[x][y] = 0;
        }
      }

      tileSize = width / tileCount;
    }

    function draw() {
      background(241, 241, 241);
      noStroke();

      // Translate origin to top-left corner
      translate(-width / 2, -height / 2);

      for (let x = 0; x < tileCount; x++) {
        for (let y = 0; y < tileCount; y++) {
          // Tile position on canvas
          let xPos = x * tileSize + tileSize / 2;
          let yPos = y * tileSize + tileSize / 2;

          // Map mouse coordinates to the top-left origin of the canvas
          let mouseAdjustedX = map(mouseX, 0, width, 0, width);
          let mouseAdjustedY = map(mouseY, 0, height, 0, height);

          // Check if tile is affected by the mouse
          let d = dist(mouseAdjustedX, mouseAdjustedY, xPos, yPos);
          if (d < tileSize * 10) {
            // Map canvas tile position to image coordinates
            let imgX = map(xPos, 0, width, 0, img.width);
            let imgY = map(yPos, 0, height, 0, img.height);

            // Get brightness from the image
            let c = img.get(int(imgX), int(imgY));
            let targetBrightness = map(brightness(c), 0, 90, 0, 1);

            // Set target size based on brightness
            targetSizes[x][y] = tileSize * targetBrightness;
          }

          // Smooth interpolation for animation
          currentSizes[x][y] = lerp(currentSizes[x][y], targetSizes[x][y], 0.1);

          // Draw the tile
          push();
          translate(xPos, yPos, 0);
          fill(0);
          ellipse(0, 0, currentSizes[x][y], currentSizes[x][y]);
          pop();
        }
      }
    }

    function windowResized() {
      resizeCanvas(windowWidth, windowHeight);

      // Adjust image size on window resize
      if (width / height > imgAspectRatio) {
        img.resize(height * imgAspectRatio, height);
      } else {
        img.resize(width, width / imgAspectRatio);
      }

      tileSize = width / tileCount;
    }

    // Attach p5.js functions to the global scope
    window.preload = preload;
    window.setup = setup;
    window.draw = draw;
    window.windowResized = windowResized;
  });
</script>
