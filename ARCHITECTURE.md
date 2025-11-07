##  **ARCHITECTURE.md**

```markdown
#  ARCHITECTURE — Real-Time Collaborative Drawing Canvas

This document describes the internal architecture, data flow, message design, and performance decisions used to build the **Real-Time Collaborative Drawing Canvas**.

---

## 1️ Data Flow Diagram — How Drawing Events Flow

User Input (mouse/touch)
↓
Client captures coordinates
↓
Emits WebSocket events via Socket.io
↓
Server receives & stores operation
↓
Server broadcasts to all connected users
↓
Clients render stroke on Canvas instantly


### Step-by-step Flow

1. The user draws on the canvas — mouse or touch input is captured.  
2. The client emits:
   - `stroke_segment` for real-time preview while drawing
   - `stroke` for the completed stroke (on mouse-up)
3. The server stores the operation in a shared `DrawingState` object.  
4. The server broadcasts the stroke to all connected users in the same room.  
5. Each client draws that stroke on its canvas immediately.  
6. Global **undo/redo** operations modify the shared history and notify all users to update their canvases.



## 2️ WebSocket Protocol — Messages Sent and Received

###  Client → Server Events
| Event | Description |
|--------|-------------|
| join room | User joins or creates a room |
| cursor move | Sends live cursor position |
| stroke segment | Sends partial stroke points for smooth live updates |
| stroke | Sends a completed stroke to be stored globally |
| global undo | Requests removal of last operation |
| global redo | Requests reapplication of last undone operation |
| clear canvas | Clears the entire shared canvas |



###  Server → Client Events
Event →  Description 

init_state →  Sends full drawing history and user list on join 
cursor_update →  Updates other users’ cursor positions 
stroke_segment →  Streams live drawing segments from others 
op applied →  Broadcasts a new completed stroke 
op_removed →  Notifies all clients that a stroke was undone 
canvas_cleared →  Clears the canvas for all users 
user joined/ user left →  Updates user list dynamically 

---

## 3️ Undo/Redo Strategy — Global History Management

Each stroke or erase action is saved as an operation object on the server:
```js
{
  id: "op_17345",
  userId: "socket123",
  type: "stroke" | "erase",
  points: [{x:0, y:0}, {x:5, y:5}, ...],
  color: "#00ff00",
  width: 4,
  timestamp: 173456232
}

## 4 Performance Decisions

**Stroke Segmentation for Smooth Updates**

Instead of sending entire strokes after completion, the client continuously sends small stroke_segment packets.

This provides real-time feedback and minimizes latency during drawing.

**In-Memory State Management**

All strokes, undo, and redo operations are stored in memory (history[], redoStack[]) instead of a database.

Ensures near-instant access and synchronization across all connected clients.

**Optimized Canvas Rendering**

The eraser uses Canvas destination-out compositing mode for efficient pixel removal.

Only changed portions of the canvas are re rendered, improving performance and frame rate.

**Event Throttling and Room Isolation**

Mouse/touch events are throttled to avoid excessive WebSocket traffic.

Room-based separation ensures updates are broadcast only to relevant users, reducing unnecessary network load.

## 5 Conflict Resolution

**Order-Based (Last-Write-Wins) Rendering**

The server assigns each drawing action a sequential order.

When users draw on overlapping areas, the latest operation overwrites previous ones.

**Server as the Single Source of Truth**

The server maintains a global history[] that defines the true canvas state.

All client updates (draw, undo, redo, clear) are synchronized through the server.

**Deterministic Replay for Consistency**

New or reconnected users receive a replay of the complete operation history in the exact same sequence.

Ensures all users see an identical canvas state.

**Eraser as a Controlled Operation**

Eraser actions are stored as normal operations using the Canvas destination-out blending mode.

This prevents pixel-level conflicts and keeps the erasing effect synchronized across clients.
