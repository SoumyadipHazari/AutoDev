# AutoDev
This is a application that automatically vibe code for you (small application) and automatically make a repo and pushes the code for you


# How to set this 

1. Download all the files and save it
2. Create a github pat (Personal Access Token) (can be a fine-grained or classic) give the required permissions like code,administration, actions and pages copy it
3. Next create a gemini api key (this code will only work for gemini api)
4. Set a secret key which is required and necessary

# How can you use it locally on your machine

1. Install python and installs the packages using ```pip install -r requirements.txt``` (I will suggest you to create a virtual environment and use it so that you don't mess up with your global python)
2. Then run ```uvicorn main:app --reload``` to run the code
3.  If you click on the localhost link you should get a ```INFO:     127.0.0.1:1415 - "GET / HTTP/1.1" 200 OK``` and also a message in a json format in the browser
 ```
{
  "message": "Task Receiver Service running. POST /ready to submit."
}
```
4. Then use curl to give your task in a json format like this using post method (You can use testfully or thunder client in vs code extension if you are not familier with curl )
   ```
   {
    "email": "xyz@abc.com",
    "secret": "I_love_anime",
    "task": "car-racing-game-new",
    "round": 1,
    "nonce": "car-game-0001",
    "brief": "Create a fun, playable 2D car racing game that runs entirely in the browser. GAME MECHANICS: 1) Top-down view racing game with player car that moves left/right using arrow keys or A/D keys, 2) Car automatically moves forward at constant speed, 3) Randomly generated opponent cars/obstacles coming from top of screen, 4) Collision detection - game over when player hits obstacle, 5) Score counter that increases over time, 6) Speed increases gradually as score goes up, 7) Road with lane markings that scroll down to create movement illusion, 8) Start/Restart button, 9) Game over screen showing final score and high score (stored in localStorage). VISUAL DESIGN: 1) Clean, colorful graphics using CSS and Canvas/SVG, 2) Smooth animations at 60fps, 3) Player car: simple top-view car shape (rectangle with rounded corners, different color), 4) Obstacle cars: various colors, 5) Road: dark grey with white lane markings, 6) Green grass on sides, 7) Score display in top corner, 8) Speed indicator, 9) Responsive design for desktop. TECHNICAL: Pure HTML5 Canvas/SVG + CSS + Vanilla JavaScript, no external libraries except maybe a simple pixel art style. Make it feel like a classic arcade racing game. Add sound effects (optional) for acceleration and collision using Web Audio API. The game must be fully playable and fun!",
    "checks": [
        "Has MIT license",
        "README.md explains game controls and rules",
        "Game loads without errors in browser",
        "Player car moves left/right with arrow keys",
        "Obstacles/cars spawn randomly from top",
        "Collision detection works correctly",
        "Score increases during gameplay",
        "Speed increases as game progresses",
        "Game over screen displays final score",
        "High score persists using localStorage",
        "Restart button works properly",
        "Road animation creates scrolling effect",
        "No backend code - runs client-side only",
        "Smooth 60fps performance",
        "Responsive layout works on desktop"
    ],
    "evaluation_url": "https://webhook.site/your url bro",
    "attachments": []
   }
   ```
 5. And wait for few seconds to minute (the time will be directly proportional to the intensity of task)
