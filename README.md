# 🎨 Real-Time Collaborative Drawing Canvas

A **multi-user real-time drawing application** built using **Vanilla JavaScript**, **HTML5 Canvas**, and **Node.js (Socket.io)**.  
Multiple users can draw together on the same shared canvas with **instant synchronization**, **live cursors**, and **global undo/redo**.

This project was developed as part of the **FLAM Technical Assignment** to demonstrate proficiency in real-time architecture, Canvas API, and WebSocket synchronization.

---

## ⚙️ Setup Instructions

1) Install Dependencies
npm install

 2)Start the Server
npm start


You should see:

Server listening on 3000

3) Open in Browser

Go to:

http://localhost:3000/


You can now draw and collaborate in real time!

How to Test with Multiple Users
Option 1 — Two Tabs on the Same Laptop

Open two different browser tabs or windows.

In both tabs, visit:

http://localhost:3000/


Draw in one — you’ll see your strokes appear instantly in the other.

Option 2 — Two Devices on the Same Wi-Fi

On the host laptop, open server/server.js and modify the last line:

server.listen(PORT, '0.0.0.0', () => console.log(`Server listening on ${PORT}`));


Run:
ipconfig

Copy your IPv4 Address 

On your friend’s device (connected to the same Wi-Fi), open:

http://172.29.14.231/:3000/

Now both users can draw together live

## Known Limitations

No Persistence - The drawing history is stored only in memory — it resets if the server restarts.
Global Undo/Redo -	Undo removes the last operation for everyone.
Network Latency -	Some lag may occur on unstable Wi-Fi networks.
No Authentication	 -Any user can join any room by entering its name.

## Time Spent on the Project

Canvas Drawing Logic	3 hrs
WebSocket Integration	3 hrs
Global Undo/Redo System	2 hrs
Real-Time Multi-User Testing	1.5 hrs
Documentation & Cleanup	1 hr

