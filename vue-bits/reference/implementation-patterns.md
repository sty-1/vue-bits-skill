# Vue-Bits Component Implementation Patterns

## Curved Loop Text Animation (SVG textPath)

The core technique: text follows an SVG `<textPath>` along a `<path>`, animated by changing `startOffset`.

```vue
<template>
  <svg viewBox="0 0 1200 300" overflow="visible" class="w-full">
    <defs>
      <path id="curve-path" d="M100,150 Q600,-100 1100,150" />
    </defs>
    <text :fill="color" :font-size="fontSize" font-weight="600">
      <textPath href="#curve-path" startOffset="0%">
        <animate
          attributeName="startOffset"
          from="0%" :to="direction === 'right' ? '100%' : '-100%'"
          :dur="`${speed}s`"
          repeatCount="indefinite"
        />
        {{ repeatedText }}
      </textPath>
    </text>
  </svg>
</template>
```

Key optimization: SVG `<animate>` runs on the compositor thread, no JS overhead.

### Alternative: JS-driven for dynamic control

```typescript
import { ref, onMounted, onUnmounted } from 'vue'

const offset = ref(0)
let rafId: number

function animate() {
  offset.value = (offset.value + speed.value / 60) % 100
  rafId = requestAnimationFrame(animate)
}

onMounted(() => { rafId = requestAnimationFrame(animate) })
onUnmounted(() => cancelAnimationFrame(rafId))
```

## Text Scramble Pattern

Character-by-character randomization using `setInterval` + progressive reveal.

```typescript
const CHARS = '!<>-_\\/[]{}—=+*^?#________'
const displayText = ref('')
let frame = 0
let queue: { from: string; to: string; start: number; end: number }[] = []

function update() {
  let output = ''
  let complete = 0
  for (let i = 0; i < queue.length; i++) {
    let { from, to, start, end } = queue[i]
    if (frame >= end) { output += to; complete++; continue }
    if (frame >= start) {
      // Random chars during scramble phase
      const progress = (frame - start) / (end - start)
      let scrambled = ''
      for (let j = 0; j < to.length; j++) {
        if (Math.random() < progress) scrambled += to[j]
        else scrambled += CHARS[Math.floor(Math.random() * CHARS.length)]
      }
      output += scrambled
    } else {
      output += from
    }
  }
  displayText.value = output
  if (complete === queue.length) return // done
  frame++
  requestAnimationFrame(() => setTimeout(update, speed.value * 1000))
}
```

## Hyperspeed Background Pattern

Canvas-based tunnel effect using perspective projection of particles.

```typescript
// Core: project 3D points to 2D with perspective divide
function project(x: number, y: number, z: number): [number, number] {
  const scale = focalLength / (focalLength + z)
  return [x * scale + centerX, y * scale + centerY]
}

// Stars move toward viewer (z decreases), reset when behind camera
function updateStars() {
  for (const star of stars) {
    star.z -= speed
    if (star.z < -focalLength) {
      star.z = maxDepth
      star.x = (Math.random() - 0.5) * spread
      star.y = (Math.random() - 0.5) * spread
    }
  }
}
```

Optimization: Pre-allocate typed arrays, use `OffscreenCanvas` when available, never read back pixel data.

## Splash Cursor Pattern

WebGL fluid simulation (simplified — full implementation uses double FBO ping-pong).

Key parameters:
- `SIM_RESOLUTION` (128): simulation grid size
- `DYE_RESOLUTION` (1440): dye texture resolution
- `DENSITY_DISSIPATION` (3.5): how fast color fades
- `SPLAT_RADIUS` (0.2): mouse click splat radius
- `SPLAT_FORCE` (6000): mouse click splat intensity

The simulation runs a Navier-Stokes solver on GPU via fragment shaders each frame.

## Performance Optimization Checklist

1. **RAF over setInterval** — `requestAnimationFrame` syncs with display refresh
2. **GPU properties only** — animate `transform`, `opacity`, `filter`; never `width`/`height`/`margin`
3. **`will-change` sparingly** — only on elements about to animate, remove when done
4. **`contain` property** — `contain: layout style paint` isolates repaint scope
5. **IntersectionObserver** — pause off-screen animations (`threshold` prop)
6. **`prefers-reduced-motion`** — disable animations when user prefers reduced motion
7. **Debounce resize/mousemove** — use `requestAnimationFrame` as natural throttle
8. **SVG over Canvas** — for simple path animations (curved text, line draw), SVG is more accessible

## CSS Custom Properties Convention

```css
.component-root {
  --vb-color-from: #6366f1;
  --vb-color-to: #a855f7;
  --vb-duration: 1.5s;
  --vb-delay: 0s;
  --vb-ease: cubic-bezier(0.16, 1, 0.3, 1);
  --vb-font-size: 1rem;
  --vb-gap: 0.5rem;
}
```

Users override these to customize without touching component internals.
