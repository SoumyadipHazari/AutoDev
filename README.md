# AutoDev
This is a application that automatically vibe code for you (small application) and automatically make a repo and pushes the code for you

Powered by Google's **Gemini 2.0 Flash** (via API) it takes the tasks and generates the necessary HTML/CSS/JS code, and pushes it to a new Github repository, and deploys it live Github Pages-all without human intervention.

# Project Architecture

The application follows a server-side process pattern where a `FastAPI` service acts as the central controller connecting the user, the AI (Gemini) and the deployment infrastructure (GitHub) 

### Core Components 
- **Task Receiver (fastAPI)**: It exposes `POST/ready` endpoint to receive task specifications, validates security secrets and manages conconcurrency using a semaphore (limited to 2 concurrent tasks by defaults)
- **AI (Google Gemini API)**:
    * **Round 1**: Generates a complete single-page application (HTML/JS/CSS), README.md, and LICENSE.
    * **Round 2+**: Performs "surgical updates" by reading the existing index.html and applying specific changes requested in the new brief.
- **Version Control Agent (GitPython)**: Manages local git repositories. It handles initializing new repos, cloning existing ones (for multi-round development), committing changes, and pushing to GitHub.
- **Deployment Manager**: Interacts with the GitHub API to create remote repositories and configure GitHub Pages for live hosting.

### Data Flow & Logic

1. **Authentication**: Every request is checked and matched with `STUDENT_SECRET` environment varible and mismatches are rejected immediately.
2. **Assest Handling**: If the request includes attachments (images/mockups), the system fetches them via HTTP or decodes Base64 data, then passes them to Gemini's vision capabilities to guide code generation.
3. **Cleaning**: Generated code is cleaned (Unicode/HTML entity decoding) before being saved to the local file system.
4.  **Feedback Generation**: After successful deployment, the system sends a webhook notification to an `evaluation_url` with the live URL and repo details, enabling automated testing.

# How It Works:

1. **Submission**: User sends a JSON payload to `/ready` with a task description and round number.
2. **Preparation**: The server sets up a local directory. If it's Round 1, it initializes a new Git repo. If it's Round 2+, it clones the existing repo to preserve history.
3. **Generation**: The system constructs a prompt (including any attached images) and sends it to Gemini. Gemini returns the raw code for the web app.
4. **Execution**: The code is saved locally. The Git agent adds all files, creates a commit (e.g., "Task - Round 1"), and pushes to the `main` branch on GitHub.
5. **Deployment**: The system calls the GitHub API to enable GitHub Pages on the main branch, making the site live.
6. **Completion**: The server notifies the evaluation webhook and returns the final URLs to the user.


# Prerequisites

Before running autodev ensure you have the followings:

1. **Python 3.10++** installed
2. **Git** is installed and configured in your machine


# How to set this 

1. Clone the repo
  ``` git clone https://github.com/soumyadiphazari/autodev ```
2. Create a github pat (Personal Access Token) (can be a fine-grained or classic) give the required permissions like code,administration, actions and pages copy it
   * Go to GitHub Settings -> Developer Settings -> Personal access tokens.
   * Create a token (Classic or Fine-grained) with **repo**, **workflow**, **admin:repo_hook**, and **pages**   permissions.
3. Next create a gemini api key (this code will only work for gemini api) [Google AI Studio](https://aistudio.google.com/).
4. Set a secret key which is required and necessary
5. Set up environment variables
   * Create a `.env` file in the root directory, copy the format from the given `env.txt` as a template and add the specific keys --
     ```GEMINI_API_KEY="your_gemini_api_key_here"
      GITHUB_TOKEN="your_github_pat_here"
      GITHUB_USERNAME="your_github_username"
      STUDENT_SECRET="my_secure_secret_key"
     ```

# How can you use it locally on your machine

1. Install python and installs the packages using ```pip install -r requirements.txt``` (I will suggest you to create a virtual environment and use it so that you don't mess up with your global python)
    * Create virtual environment `python -m venv venv`
    * Activate it `venv/Scripts/activate`
    * Install requitrements `pip install -r requirements.txt`
3. Then run ```uvicorn main:app --reload``` to run the code
4.  If you click on the localhost link you should get a ```INFO: 127.0.0.1:1415 - "GET / HTTP/1.1" 200 OK``` and also a message in a json format in the browser
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

# Run on Hugging Face Spaces

This application is ready to run on Hugging Face Spaces using using the Docker SDK.

1. **Create a New Space**
   * Go to [Hugging Face Spaces](https://huggingface.co/spaces) and create a new Space.
   * Select **Docker** as the Space SDK.
   * Choose "Blank" as the template.
2. **Upload the code**
   * Push all the files from this repositary to your new space.
3. **Configure Secrets**
   * Navigate to the **Settings** tab of your Space.
    * Scroll down to the **Variables and secrets** section.
    * Add the following **New Secrets** (copy the values from your local `.env` file):
        * `GEMINI_API_KEY`
        * `GITHUB_TOKEN`
        * `GITHUB_USERNAME`
        * `STUDENT_SECRET`
4. **Launch**:
   * The Space will automatically build and start.
#  Techstack 
- Language: **Python 3.10+** (referred in `dockerfile`)
- Web Framework: **FastAPI** (referenced in `requirements.txt` and imported in `main.py`)
- ASGI Server: **Uvicorn**
- AI Provider: **gemini-2.5-flash-preview-05-20** (Uses standard HTTP requests via `httpx` rather than the official SDK to interact with the API.)
- Validation: **Pydantic** (used for data modeling and environment settings)
- Settings management: **pydantic-settings** and **python-dotenv** (for managing `.env` file)
- Version Control System: **GIT** managed by **GitPython**
- External APIs: GitHub API (responsible for creating)
