# Examples

## Basic Layouts

### Simple Header-Content-Footer

```typescript
import { Layout } from '@basementuniverse/layout';

const layout = new Layout({
  root: {
    id: 'root',
    type: 'stack',
    direction: 'vertical',
    children: [
      { id: 'header', type: 'leaf', size: { y: '60px' } },
      { id: 'content', type: 'leaf' }, // Auto-fills remaining space
      { id: 'footer', type: 'leaf', size: { y: '40px' } },
    ],
  },
});

// Update for canvas size
layout.update({ x: 1024, y: 768 });

// Render
const header = layout.get('header');
const content = layout.get('content');
const footer = layout.get('footer');

if (header) ctx.fillRect(header.left, header.top, header.width, header.height);
if (content) ctx.fillRect(content.left, content.top, content.width, content.height);
if (footer) ctx.fillRect(footer.left, footer.top, footer.width, footer.height);
```

---

### Sidebar Layout

```typescript
const layout = new Layout({
  root: {
    id: 'root',
    type: 'stack',
    direction: 'horizontal',
    gap: '10px',
    padding: { x: '10px', y: '10px' },
    children: [
      {
        id: 'sidebar',
        type: 'leaf',
        size: { x: '200px' },
      },
      {
        id: 'main',
        type: 'leaf', // Auto-fills remaining width
      },
    ],
  },
});
```

---

## Game UI Layouts

### HUD with Score and Health

```typescript
const layout = new Layout({
  root: {
    id: 'hud',
    type: 'dock',
    topLeft: {
      id: 'score',
      type: 'leaf',
      size: { x: '150px', y: '40px' },
      offset: { x: '10px', y: '10px' },
    },
    topRight: {
      id: 'health',
      type: 'leaf',
      size: { x: '200px', y: '30px' },
      offset: { x: '-10px', y: '10px' },
    },
    bottomCenter: {
      id: 'message',
      type: 'leaf',
      size: { x: '400px', y: '60px' },
      offset: { x: '0px', y: '-20px' },
    },
  },
});

layout.update({ x: 1920, y: 1080 });

// Render HUD elements
function renderHUD() {
  const score = layout.get('score');
  const health = layout.get('health');
  const message = layout.get('message');

  if (score) {
    ctx.fillStyle = 'white';
    ctx.fillText(`Score: ${gameScore}`, score.left + 10, score.center.y);
  }

  if (health) {
    ctx.fillStyle = 'red';
    ctx.fillRect(health.left, health.top, health.width * (currentHealth / maxHealth), health.height);
  }

  if (message?.visible) {
    ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
    ctx.fillRect(message.left, message.top, message.width, message.height);
    ctx.fillStyle = 'white';
    ctx.textAlign = 'center';
    ctx.fillText(currentMessage, message.center.x, message.center.y);
  }
}
```

---

### Menu System

```typescript
const layout = new Layout({
  root: {
    id: 'menu',
    type: 'dock',
    center: {
      id: 'menuPanel',
      type: 'stack',
      direction: 'vertical',
      gap: '15px',
      size: { x: '300px', y: 'auto' },
      padding: { x: '20px', y: '20px' },
      children: [
        { id: 'title', type: 'leaf', size: { y: '60px' } },
        { id: 'play', type: 'leaf', size: { y: '50px' } },
        { id: 'options', type: 'leaf', size: { y: '50px' } },
        { id: 'quit', type: 'leaf', size: { y: '50px' } },
      ],
    },
  },
});

layout.update({ x: 1280, y: 720 });

// Render menu
function renderMenu() {
  const panel = layout.get('menuPanel');
  if (panel) {
    ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
    ctx.fillRect(panel.left, panel.top, panel.width, panel.height);
  }

  const buttons = ['title', 'play', 'options', 'quit'];
  buttons.forEach(id => {
    const button = layout.get(id);
    if (button) {
      ctx.fillStyle = id === hoveredButton ? '#444' : '#222';
      ctx.fillRect(button.left, button.top, button.width, button.height);
      ctx.fillStyle = 'white';
      ctx.textAlign = 'center';
      ctx.fillText(id.toUpperCase(), button.center.x, button.center.y);
    }
  });
}
```

---

### Inventory Grid

```typescript
const GRID_SIZE = 4;
const inventoryChildren: LayoutNodeOptions[] = [];

for (let i = 0; i < GRID_SIZE * GRID_SIZE; i++) {
  inventoryChildren.push({
    id: `slot-${i}`,
    type: 'leaf',
    size: { x: '60px', y: '60px' },
  });
}

const layout = new Layout({
  root: {
    id: 'inventory',
    type: 'dock',
    bottomRight: {
      id: 'inventoryPanel',
      type: 'stack',
      direction: 'vertical',
      gap: '5px',
      offset: { x: '-20px', y: '-20px' },
      children: Array.from({ length: GRID_SIZE }, (_, row) => ({
        id: `row-${row}`,
        type: 'stack',
        direction: 'horizontal',
        gap: '5px',
        children: inventoryChildren.slice(row * GRID_SIZE, (row + 1) * GRID_SIZE),
      })),
    },
  },
});
```

---

## Advanced Layouts

### Responsive Dashboard

```typescript
class Dashboard {
  private layout: Layout;
  private compactMode = false;

  constructor() {
    this.layout = new Layout({
      root: {
        id: 'root',
        type: 'stack',
        direction: 'vertical',
        padding: { x: '10px', y: '10px' },
        children: [
          {
            id: 'topBar',
            type: 'stack',
            direction: 'horizontal',
            gap: '10px',
            size: { y: '50px' },
            children: [
              { id: 'logo', type: 'leaf', size: { x: '120px' } },
              { id: 'search', type: 'leaf' },
              { id: 'profile', type: 'leaf', size: { x: '40px' } },
            ],
          },
          {
            id: 'mainArea',
            type: 'stack',
            direction: 'horizontal',
            gap: '10px',
            children: [
              { id: 'sidebar', type: 'leaf', size: { x: '200px' } },
              {
                id: 'content',
                type: 'stack',
                direction: 'vertical',
                gap: '10px',
                children: [
                  { id: 'toolbar', type: 'leaf', size: { y: '40px' } },
                  { id: 'viewport', type: 'leaf' },
                  { id: 'statusBar', type: 'leaf', size: { y: '30px' } },
                ],
              },
              { id: 'properties', type: 'leaf', size: { x: '250px' } },
            ],
          },
        ],
      },
    });
  }

  update(canvasWidth: number, canvasHeight: number) {
    // Switch to compact mode on small screens
    const wasCompact = this.compactMode;
    this.compactMode = canvasWidth < 1024;

    if (wasCompact !== this.compactMode) {
      // Hide panels in compact mode
      this.layout.setActivated('sidebar', !this.compactMode);
      this.layout.setActivated('properties', !this.compactMode);
    }

    this.layout.update({ x: canvasWidth, y: canvasHeight });
  }
}
```

---

### Dialog System

```typescript
class DialogManager {
  private layout: Layout;
  private dialogVisible = false;

  constructor() {
    this.layout = new Layout({
      root: {
        id: 'overlay',
        type: 'dock',
        center: {
          id: 'dialog',
          type: 'stack',
          direction: 'vertical',
          size: { x: '500px', y: '300px' },
          padding: { x: '20px', y: '20px' },
          gap: '15px',
          visible: false,
          children: [
            { id: 'dialogTitle', type: 'leaf', size: { y: '40px' } },
            { id: 'dialogContent', type: 'leaf' },
            {
              id: 'dialogButtons',
              type: 'stack',
              direction: 'horizontal',
              gap: '10px',
              size: { y: '40px' },
              align: 'end',
              children: [
                { id: 'cancelButton', type: 'leaf', size: { x: '100px' } },
                { id: 'confirmButton', type: 'leaf', size: { x: '100px' } },
              ],
            },
          ],
        },
      },
    });
  }

  showDialog() {
    this.layout.setVisibility('dialog', true);
    this.dialogVisible = true;
  }

  hideDialog() {
    this.layout.setVisibility('dialog', false);
    this.dialogVisible = false;
  }

  update(canvasSize: { x: number; y: number }) {
    this.layout.update(canvasSize);
  }

  render(ctx: CanvasRenderingContext2D) {
    if (!this.dialogVisible) return;

    // Semi-transparent overlay
    const overlay = this.layout.get('overlay');
    if (overlay) {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
      ctx.fillRect(overlay.left, overlay.top, overlay.width, overlay.height);
    }

    // Dialog background
    const dialog = this.layout.get('dialog');
    if (dialog) {
      ctx.fillStyle = 'white';
      ctx.fillRect(dialog.left, dialog.top, dialog.width, dialog.height);
      ctx.strokeStyle = 'black';
      ctx.strokeRect(dialog.left, dialog.top, dialog.width, dialog.height);
    }

    // Render dialog contents
    // ... render title, content, buttons
  }
}
```

---

### Aspect Ratio Constrained Viewport

```typescript
const layout = new Layout({
  root: {
    id: 'root',
    type: 'dock',
    center: {
      id: 'gameViewport',
      type: 'leaf',
      size: { x: '90%', y: '90%' },
      aspectRatio: 16 / 9, // Force 16:9 aspect ratio
    },
  },
});

layout.update({ x: 1920, y: 1080 });

// Viewport will maintain 16:9 ratio regardless of canvas size
const viewport = layout.get('gameViewport');
if (viewport) {
  // Render game content with letterboxing/pillarboxing as needed
  ctx.fillStyle = 'black';
  ctx.fillRect(0, 0, 1920, 1080); // Entire canvas

  // Game content in viewport
  ctx.save();
  ctx.beginPath();
  ctx.rect(viewport.left, viewport.top, viewport.width, viewport.height);
  ctx.clip();

  renderGame(viewport);

  ctx.restore();
}
```

---

## Dynamic Layouts

### Toggle-able Panels

```typescript
const layout = new Layout({
  root: {
    id: 'root',
    type: 'stack',
    direction: 'vertical',
    children: [
      { id: 'header', type: 'leaf', size: { y: '60px' } },
      {
        id: 'body',
        type: 'stack',
        direction: 'horizontal',
        gap: '10px',
        children: [
          { id: 'leftPanel', type: 'leaf', size: { x: '200px' } },
          { id: 'center', type: 'leaf' },
          { id: 'rightPanel', type: 'leaf', size: { x: '200px' } },
        ],
      },
    ],
  },
});

// Toggle panels
function toggleLeftPanel() {
  layout.setActivated('leftPanel'); // Toggle (no second argument)
}

function toggleRightPanel() {
  layout.setActivated('rightPanel');
}

// When panels are deactivated, center expands to fill the space
```

---

### Animated Transitions

```typescript
class AnimatedLayout {
  private layout: Layout;
  private targetSidebarWidth = 200;
  private currentSidebarWidth = 200;
  private animationSpeed = 5;

  update(deltaTime: number, canvasSize: { x: number; y: number }) {
    // Animate sidebar width
    if (Math.abs(this.currentSidebarWidth - this.targetSidebarWidth) > 1) {
      const diff = this.targetSidebarWidth - this.currentSidebarWidth;
      this.currentSidebarWidth += diff * this.animationSpeed * deltaTime;

      // Recreate layout with new sidebar width (layouts are immutable)
      this.layout = new Layout({
        root: {
          id: 'root',
          type: 'stack',
          direction: 'horizontal',
          children: [
            { id: 'sidebar', type: 'leaf', size: { x: `${this.currentSidebarWidth}px` } },
            { id: 'content', type: 'leaf' },
          ],
        },
      });
    }

    this.layout.update(canvasSize);
  }

  collapseSidebar() {
    this.targetSidebarWidth = 50;
  }

  expandSidebar() {
    this.targetSidebarWidth = 200;
  }
}
```

Note: For smooth animations, consider recreating the layout with interpolated values or use visibility/activation for instant transitions.
