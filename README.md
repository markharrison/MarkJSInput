# MarkJSInput

A lightweight, generic input handler for HTML Canvas applications that captures keyboard, mouse, touch, and gamepad input using a publisher-subscriber pattern.

## Features

-   **Multi-Input Support**: Handles keyboard, mouse, touch, and gamepad input seamlessly
-   **Publisher-Subscriber Pattern**: Multiple subscribers can receive input events simultaneously
-   **Canvas Coordinate Mapping**: Automatically converts screen coordinates to canvas coordinates with scaling support
-   **External Animation Loop**: Designed to integrate with your existing game/animation loop
-   **Clean Resource Management**: Proper cleanup and event listener management
-   **Zero Dependencies**: Pure JavaScript ES6 module

## Installation

```bash
npm install @markharrison/markjsinput --save
```

## Quick Start

```javascript
import { MarkJSInput } from '@markharrison/markjsinput';

const canvas = document.getElementById('gameCanvas');
const input = new MarkJSInput(canvas);

const gameSubscriber = {
    onKeyDown(event) {
        console.log('Key pressed:', event.key);
    },
    onMouseMove(x, y) {
        console.log('Mouse at:', x, y);
    },
    onTouchStart(x, y) {
        console.log('Touch at:', x, y);
    },
    onGamepadButton(buttonIndex) {
        console.log('Button:', buttonIndex);
    },
};

input.subscribe(gameSubscriber);

function gameLoop() {
    input.update(); // Update gamepad state
    // Your game logic here
    requestAnimationFrame(gameLoop);
}
gameLoop();
```

## Documentation

📖 **Complete documentation and API reference**: [markjsinput.md](markjsinput.md)

## Test Application

🎮 **Interactive test application**: Open `index.html` in your browser to see MarkJSInput in action with real-time input visualization and testing of all supported input methods.

![MarkJSInputShowcase](https://github.com/user-attachments/assets/f9afdb90-ab55-4c52-9b00-82c476bd66d1)

## Browser Support

-   Modern browsers supporting ES6 modules
-   Canvas API support
-   Gamepad API support (where available)

## License

MIT License - See [LICENSE](LICENSE) file for details.
