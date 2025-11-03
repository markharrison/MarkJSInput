# MarkJSInput Developer Documentation

## Overview

**MarkJSInput** is a generic input handler for HTML Canvas applications. It captures keyboard, mouse, touch, and gamepad input, and broadcasts events to subscribers using a publisher-subscriber pattern.

**Key Features:**

-   **Publisher-Subscriber Pattern**: Multiple subscribers can receive input events simultaneously
-   **Multi-Input Support**: Handles keyboard, mouse, touch, and gamepad input
-   **Canvas Coordinate Mapping**: Automatically converts screen coordinates to canvas coordinates with scaling support
-   **External Animation Loop**: Designed to integrate with your existing game/animation loop
-   **Clean Resource Management**: Proper cleanup and event listener management

## Installation & Basic Setup

### 1. Install the Library

```bash
npm install markjsinput@latest --save
```

### 2. Import the Library

```javascript
import { MarkJSInput } from 'markjsinput';
```

**For local development:**

```javascript
import { MarkJSInput } from './markjsinput.js';
```

### 3. Initialize with Canvas

```javascript
// Get your canvas element
const canvas = document.getElementById('myCanvas');

// Create the input handler
const input = new MarkJSInput(canvas);
```

### 4. Create a Subscriber

Create an object with event handler methods:

```javascript
const gameSubscriber = {
    // Keyboard events
    onKeyDown(event) {
        console.log('Key pressed:', event.key);
    },

    onKeyUp(event) {
        console.log('Key released:', event.key);
    },

    // Mouse events
    onMouseMove(x, y) {
        console.log('Mouse moved to:', x, y);
    },

    onMouseDown(x, y, button) {
        console.log('Mouse button pressed:', button, 'at', x, y);
    },

    onMouseUp(x, y, button) {
        console.log('Mouse button released:', button, 'at', x, y);
    },

    onMouseClick(x, y, button) {
        console.log('Mouse clicked:', button, 'at', x, y);
    },

    // Touch events
    onTouchStart(x, y) {
        console.log('Touch started at:', x, y);
    },

    onTouchMove(x, y) {
        console.log('Touch moved to:', x, y);
    },

    onTouchEnd(x, y) {
        console.log('Touch ended at:', x, y);
    },

    // Gamepad events
    onGamepadConnected(event) {
        console.log('Gamepad connected:', event);
    },

    onGamepadButton(buttonIndex) {
        console.log('Gamepad button pressed:', buttonIndex);
    },

    onGamepadAxis(axisData) {
        console.log('Gamepad thumbstick moved:', axisData);
    },
};
```

### 5. Subscribe to Events

```javascript
// Subscribe to input events
const subscription = input.subscribe(gameSubscriber);

// Later, unsubscribe if needed
subscription.unsubscribe();
```

### 6. Update Loop Integration

```javascript
function gameLoop() {
    // Update gamepad state (call once per frame)
    input.update();

    // Your game logic here
    // ...

    requestAnimationFrame(gameLoop);
}

gameLoop();
```

### 7. Cleanup

```javascript
// When done, clean up resources
input.destroy();
```

## API Reference

### Constructor

#### `new MarkJSInput(canvas)`

Creates a new input handler instance.

**Parameters:**

-   `canvas` (HTMLCanvasElement): The canvas element to attach input listeners to

**Example:**

```javascript
const canvas = document.getElementById('gameCanvas');
const input = new MarkJSInput(canvas);
```

### Methods

#### `subscribe(subscriber)`

Registers a subscriber to receive input events.

**Parameters:**

-   `subscriber` (Object): Object containing event handler methods

**Returns:**

-   `Object`: Subscription object with `unsubscribe()` method

**Example:**

```javascript
const subscription = input.subscribe({
    onKeyDown(event) {
        /* handle key press */
    },
});
```

#### `unsubscribe(subscriber)`

Removes a subscriber from receiving input events.

**Parameters:**

-   `subscriber` (Object): The subscriber object to remove

#### `update()`

Updates gamepad state. Call this once per frame in your animation loop.

**Example:**

```javascript
function gameLoop() {
    input.update(); // Update gamepad
    // ... rest of game logic
}
```

#### `getCanvasCoordinates(clientX, clientY)`

Converts screen/viewport coordinates to canvas coordinate system.

**Parameters:**

-   `clientX` (Number): Screen X coordinate
-   `clientY` (Number): Screen Y coordinate

**Returns:**

-   `Object`: `{x, y}` coordinates in canvas space

**Example:**

```javascript
const canvasPos = input.getCanvasCoordinates(event.clientX, event.clientY);
console.log('Canvas coordinates:', canvasPos.x, canvasPos.y);
```

#### `destroy()`

Cleans up all event listeners and resources.

**Example:**

```javascript
// Clean up when done
input.destroy();
```

### Properties

#### `keys`

Object containing current keyboard state. Keys are mapped by `event.key` values.

**Example:**

```javascript
if (input.keys['w'] || input.keys['ArrowUp']) {
    // Move player up
}
```

#### `gamepad`

Current gamepad object (if connected), or `null`.

**Example:**

```javascript
if (input.gamepad) {
    console.log('Gamepad connected:', input.gamepad.id);
}
```

## Event Handler Reference

### Keyboard Events

#### `onKeyDown(event)`

-   **event**: KeyboardEvent object
-   Fired when a key is pressed down

#### `onKeyUp(event)`

-   **event**: KeyboardEvent object
-   Fired when a key is released

### Mouse Events

All mouse events provide coordinates in canvas space (automatically converted).

#### `onMouseMove(x, y)`

-   **x, y**: Canvas coordinates
-   Fired when mouse moves over canvas

#### `onMouseDown(x, y, button)`

-   **x, y**: Canvas coordinates
-   **button**: Mouse button index (0=left, 1=middle, 2=right)
-   Fired when mouse button is pressed

#### `onMouseUp(x, y, button)`

-   **x, y**: Canvas coordinates
-   **button**: Mouse button index
-   Fired when mouse button is released

#### `onMouseClick(x, y, button)`

-   **x, y**: Canvas coordinates
-   **button**: Mouse button index
-   Fired on mouse click (after mousedown + mouseup)

#### `onMouseEnter(x, y)`

-   **x, y**: Canvas coordinates
-   Fired when mouse enters canvas area

### Touch Events

Touch events only track the first touch point.

#### `onTouchStart(x, y)`

-   **x, y**: Canvas coordinates
-   Fired when touch begins

#### `onTouchMove(x, y)`

-   **x, y**: Canvas coordinates
-   Fired when touch moves

#### `onTouchEnd(x, y)`

-   **x, y**: Canvas coordinates
-   Fired when touch ends

### Gamepad Events

#### `onGamepadConnected(event)`

-   **event**: GamepadEvent object
-   Fired when a gamepad is connected

#### `onGamepadButton(buttonIndex)`

-   **buttonIndex**: Index of pressed button
-   Fired when gamepad button is pressed

#### `onGamepadAxis(axisData)`

-   **axisData**: Object containing thumbstick data
    -   `leftStick: {x, y}`: Left thumbstick values (-1 to 1)
    -   `rightStick: {x, y}`: Right thumbstick values (-1 to 1)
    -   `deadZone`: Deadzone threshold (0.3)
-   Fired when thumbstick position changes significantly

## Usage Examples

### Simple Game Character Movement

```javascript
const player = { x: 100, y: 100, speed: 5 };

const gameControls = {
    onKeyDown(event) {
        // Handle key presses for immediate response
    },

    // Use the keys object for continuous movement
    update() {
        if (input.keys['w'] || input.keys['ArrowUp']) {
            player.y -= player.speed;
        }
        if (input.keys['s'] || input.keys['ArrowDown']) {
            player.y += player.speed;
        }
        if (input.keys['a'] || input.keys['ArrowLeft']) {
            player.x -= player.speed;
        }
        if (input.keys['d'] || input.keys['ArrowRight']) {
            player.x += player.speed;
        }
    },
};

input.subscribe(gameControls);

function gameLoop() {
    input.update(); // Update gamepad
    gameControls.update(); // Update movement
    // ... render game
    requestAnimationFrame(gameLoop);
}
```

### Canvas Drawing Application

```javascript
let isDrawing = false;
let lastPos = { x: 0, y: 0 };

const drawingApp = {
    onMouseDown(x, y, button) {
        if (button === 0) {
            // Left mouse button
            isDrawing = true;
            lastPos = { x, y };
        }
    },

    onMouseMove(x, y) {
        if (isDrawing) {
            // Draw line from last position to current
            drawLine(lastPos.x, lastPos.y, x, y);
            lastPos = { x, y };
        }
    },

    onMouseUp(x, y, button) {
        if (button === 0) {
            isDrawing = false;
        }
    },

    // Support touch devices
    onTouchStart(x, y) {
        isDrawing = true;
        lastPos = { x, y };
    },

    onTouchMove(x, y) {
        if (isDrawing) {
            drawLine(lastPos.x, lastPos.y, x, y);
            lastPos = { x, y };
        }
    },

    onTouchEnd(x, y) {
        isDrawing = false;
    },
};

input.subscribe(drawingApp);
```

### Multiple Subscribers Example

```javascript
// UI system handles menus
const uiSystem = {
    onMouseClick(x, y, button) {
        if (this.checkButtonClick(x, y)) {
            console.log('Button clicked!');
        }
    },
};

// Game system handles gameplay
const gameSystem = {
    onKeyDown(event) {
        if (event.key === ' ') {
            this.jump();
        }
    },
};

// Physics system needs mouse for interactions
const physicsSystem = {
    onMouseDown(x, y, button) {
        this.startDrag(x, y);
    },
};

// All systems receive events simultaneously
input.subscribe(uiSystem);
input.subscribe(gameSystem);
input.subscribe(physicsSystem);
```

### Gamepad Support Example

```javascript
const gamepadControls = {
    onGamepadButton(buttonIndex) {
        // Standard gamepad button mapping
        switch (buttonIndex) {
            case 0: // A button (cross on PlayStation)
                player.jump();
                break;
            case 1: // B button (circle on PlayStation)
                player.run();
                break;
            case 2: // X button (square on PlayStation)
                player.attack();
                break;
            case 3: // Y button (triangle on PlayStation)
                player.interact();
                break;
        }
    },

    onGamepadAxis(axisData) {
        const { leftStick, rightStick, deadZone } = axisData;

        // Left stick for movement
        if (Math.abs(leftStick.x) > deadZone) {
            player.x += leftStick.x * player.speed;
        }
        if (Math.abs(leftStick.y) > deadZone) {
            player.y += leftStick.y * player.speed;
        }

        // Right stick for camera/aiming
        if (Math.abs(rightStick.x) > deadZone) {
            camera.angle += rightStick.x * camera.sensitivity;
        }
    },
};

input.subscribe(gamepadControls);
```

## Best Practices

### 1. Use Appropriate Event Types

-   **onKeyDown/onKeyUp**: For actions that happen once per key press
-   **keys object**: For continuous actions (movement, held buttons)
-   **Mouse events**: For UI interactions and drawing
-   **Touch events**: Ensure mobile compatibility

### 2. Handle Multiple Input Methods

```javascript
const controls = {
    // Support both keyboard and gamepad for movement
    onKeyDown(event) {
        if (event.key === ' ') this.jump();
    },

    onGamepadButton(buttonIndex) {
        if (buttonIndex === 0) this.jump(); // A button
    },

    jump() {
        // Actual jump logic here
        console.log('Player jumped!');
    },
};
```

### 3. Coordinate System Considerations

MarkJSInput automatically handles canvas coordinate conversion, but be aware:

-   Coordinates are clamped to canvas bounds
-   Scaling is automatically handled
-   Coordinates are rounded to integers

### 4. Resource Management

Always call `destroy()` when cleaning up:

```javascript
// In cleanup/shutdown code
input.destroy();
```

### 5. Gamepad Polling

Remember to call `update()` in your animation loop for gamepad support:

```javascript
function gameLoop() {
    input.update(); // Essential for gamepad
    // ... rest of game logic
    requestAnimationFrame(gameLoop);
}
```

## Troubleshooting

### Common Issues

**Gamepad not working:**

-   Ensure you're calling `input.update()` in your game loop
-   Check that gamepad is properly connected
-   Test with `navigator.getGamepads()` in browser console

**Mouse coordinates seem wrong:**

-   MarkJSInput automatically handles canvas scaling
-   Ensure canvas has proper CSS dimensions
-   Check `canvas.width` vs `canvas.style.width`

**Events not firing:**

-   Verify subscriber object has correct method names
-   Check that canvas element is properly passed to constructor
-   Ensure canvas has focus for keyboard events

**Touch events not working on mobile:**

-   MarkJSInput prevents default touch behavior
-   Ensure viewport meta tag is set correctly
-   Test with actual mobile device, not desktop browser

### Canvas Scaling Example

```html
<!-- HTML -->
<canvas id="gameCanvas" width="800" height="600" style="width: 400px; height: 300px;"></canvas>
```

```javascript
// MarkJSInput automatically handles the 2x scaling in this example
// Mouse coordinates will be correctly mapped to the 800x600 internal canvas size
const input = new MarkJSInput(document.getElementById('gameCanvas'));
```
