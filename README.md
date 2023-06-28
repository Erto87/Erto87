<h1>I'm Tapio <strong>**Game Developer**</strong></h1>
<h3>Hi there 👋 My name is Tapio Kettunen and I am an aspiring game programmer. I have been delving into the fascinating world of game development and have been focusing on learning Unity game engine and C# programming language.</h3>

<h2>👨‍💻 My School Project (Working In a Team)</h2>
<details>
<summary><h3>Title: Dark Rooms https://github.com/Erto87/ProjectHorror</h3><img alt="DarkRooms" width="500px" src="https://raw.githubusercontent.com/Erto87/Erto87/main/DarkRooms.png"/></summary>

<details>
<summary><h3>Example code I made for examining game objects in game</h3></summary>
  
```
public class Examine : MonoBehaviour
{
    Camera mainCam;//Camera Object Will Be Placed In Front Of
    GameObject clickedObject;//Currently Clicked Object
    public PauseAndInventoryMenu pauseAndInventoryMenu;

    //Holds Original Postion And Rotation So The Object Can Be Replaced Correctly
    Vector3 originaPosition;
    Vector3 originalRotation;

    //If True Allow Rotation Of Object
    public bool examineMode;
    // Controls the speed of the zoom
    private float scrollSpeed = 10f;
    

    void Start()
    {
        mainCam = Camera.main;// Get the main camera in the scene
        examineMode = false;// Set examine mode to false at the start
        pauseAndInventoryMenu = FindObjectOfType<PauseAndInventoryMenu>();
    }

    private void Update()
    {
        //ClickObject();//Decide What Object To Examine

        TurnObject();//Allows Object To Be Rotated

        ExitExamineMode();//Returns Object To Original Postion

        ZoomCamera();// Zoom camera
    }

    void ZoomCamera()
    {
        if (examineMode == true)  // Only zoom if in examine mode
        {
            if (mainCam.orthographic)// If the camera is in orthographic mode
            {
                mainCam.orthographicSize -= Input.GetAxis("Mouse ScrollWheel") * scrollSpeed;// Zoom in/out based on scroll wheel input
            }
            else 
            {
                mainCam.fieldOfView -= Input.GetAxis("Mouse ScrollWheel") * scrollSpeed; // Zoom in/out based on scroll wheel input
            }
        }
    }

    void ClickObject()
    {
        if (Input.GetKeyDown(KeyCode.F) && examineMode == false)// Check if F key is pressed and not already in examine mode
        {
            // Find the center of the screen in world space
            Vector3 screenCenter = new Vector3(mainCam.pixelWidth / 2f, mainCam.pixelHeight / 2f, 1f);
            Vector3 worldCenter = mainCam.ScreenToWorldPoint(screenCenter);

            // Cast a ray from the center of the screen forward
            RaycastHit hit;
            Ray ray = new Ray(worldCenter, mainCam.transform.forward);
            //Ray ray = mainCam.ScreenPointToRay(Input.mousePosition);

            // If the ray hits an object with the "Item" tag
            if (Physics.Raycast(ray, out hit))
            {
                if (hit.transform.tag == "Item")
                {
                    //ClickedObject Will Be The Object Hit By The Raycast
                    clickedObject = hit.transform.gameObject;

                    //Save The Original Postion And Rotation
                    originaPosition = clickedObject.transform.position;
                    originalRotation = clickedObject.transform.rotation.eulerAngles;

                    //Now Move Object In Front Of Camera
                    clickedObject.transform.position = mainCam.transform.position + (transform.forward * 1f);

                    //Pause The Game
                    Time.timeScale = 0;

                    //Turn Examine Mode To True
                    examineMode = true;
                }
            }
        }
    }

    void TurnObject()
    {
        if (Input.GetMouseButton(0) && examineMode)// Check if left mouse button is held down and in examine mode
        {
            float rotationSpeed = 15;

            // Get mouse movement and rotate the object accordingly
            float xAxis = Input.GetAxis("Mouse X") * rotationSpeed;
            float yAxis = Input.GetAxis("Mouse Y") * rotationSpeed;

            clickedObject.transform.Rotate(Vector3.up, -xAxis, Space.World);
            clickedObject.transform.Rotate(Vector3.right, yAxis, Space.World);
        }
    }

    void ExitExamineMode()
    {
        // If the player is currently examining an object and there is no other object currently selected,
        // or if the right mouse button is being held down and the game is not paused, continue with the examine mode.
        if (examineMode && clickedObject == null || Input.GetMouseButton(1) && pauseAndInventoryMenu.gameIsPaused == false)
        {
            mainCam.fieldOfView = 60f;
            //Reset Object To Original Position

            if (clickedObject != null)
            {
                clickedObject.transform.position = originaPosition;
                clickedObject.transform.eulerAngles = originalRotation;
            }
           
            //Unpause Game
            Time.timeScale = 1;

            //Return To Normal State
            examineMode = false;

        }
    }
}
```

</details>

Genre: Horror survival

Reference games: Resident Evil, Amnesia

Game Elements: Light & Dark environment, flashlight, inventory, items to collect

Player: SinglePlayer

Technical Form: 3D 1920x1080

View: First Person

Version Control: Unity 2022.1.22f1

Platform: PC

Language: C#

Device: PC

Gameplay: In this game, the player finds themselves trapped in a haunted hotel, mansion, or house and must find a way to escape while evading a terrifying presence that is determined to kill them. The player must utilize light sources, such as a flashlight, to navigate through the environment and gather essential items to aid their progression.

Controls: The player will use the WASD keys for movement and the mouse for camera control. Controller support will also be implemented.

Story: The player assumes the role of a visitor who becomes trapped inside the hotel, mansion, or house due to an unknown force that prevents them from leaving.

Game Mechanics:

Stealth: The player must avoid detection by the malevolent presence by utilizing environmental hiding spots and cover.
Puzzle Solving: To progress and find an escape route, the player must solve various puzzles.
Exploration: The player needs to thoroughly explore the environment to unveil the building's history and discover valuable items.
UI Design: The game's user interface (UI) will have a minimalist design.

This Game Design Document outlines the key aspects of the Project Horror Game, including its genre, references, gameplay mechanics, controls, story, and technical details. It serves as a roadmap and reference for the development team to ensure a cohesive and immersive horror survival experience.
</details>

<details>
<summary><h3>Title: FPH First Person Hockey https://github.com/Erto87/FPH-First-Person-Hockey</h3><img alt="FPH" width="500px" src="https://raw.githubusercontent.com/Erto87/Erto87/main/FPH.png"/></summary>

<details>
<summary><h3>Example code I made for following game time</h3></summary>
  
```
public class TimerController : NetworkBehaviour
{
    public static TimerController instance;  // Singleton instance of the TimerController
    public TextMeshPro timerText, timerText2;  // Text objects to display the timer
    public float timer = 300.0f;  // Initial timer value
    //public bool isTimer = false;
    public NetworkVariable<bool> isTimer = new NetworkVariable<bool>(false, NetworkVariableReadPermission.Everyone);  // Network variable to synchronize the timer across the network

    //public NetworkVariable<bool> playReplay = new NetworkVariable<bool>(false, NetworkVariableReadPermission.Everyone);
    public bool playReplay;  // Bool to control replay functionality
    public bool playReplayOnce;  // Bool to ensure replay is started only once

    public GameObject playerCamera;  // Reference to the player camera object
    public NetworkVariable<bool> endOfPeriod = new NetworkVariable<bool>(false, NetworkVariableReadPermission.Everyone);  // Network variable to indicate the end of the period

    void Start()
    {
        playReplayOnce = false;
        //StartTimer();
        instance = this;  // Assign the singleton instance
        //isTimer.Value = false;
    }

    void Update()
    {
        HandleTimer();  // Update the timer logic
    }

    // Display the current time on the timer text objects
    void DisplayTime()
    {
        int minutes = Mathf.FloorToInt(timer / 60.0f);  // Calculate the minutes
        int seconds = Mathf.FloorToInt(timer - minutes * 60);  // Calculate the seconds
        timerText.text = string.Format("{0:00}:{1:00}", minutes, seconds);  // Update the timer text
        timerText2.text = string.Format("{0:00}:{1:00}", minutes, seconds);  // Update the secondary timer text
    }

    // Start the timer
    public void StartTimer()
    {
        playReplayOnce = false;
        playReplay = false;
        timerText.color = Color.green;  // Set the timer text color to green
        timerText2.color = Color.green;  // Set the secondary timer text color to green
        if (IsServer)
        {
            isTimer.Value = true;  // Set the isTimer network variable to true on the server
        }
    }

    // Stop the timer
    public void StopTimer()
    {
        timerText.color = Color.red;  // Set the timer text color to red
        timerText2.color = Color.red;  // Set the secondary timer text color to red
        if (IsServer)
        {
            isTimer.Value = false;  // Set the isTimer network variable to false on the server
        }
        if (!playReplayOnce)
        {
            StartCoroutine(StartReplay());  // Start the replay coroutine
            StartCoroutine(StopReplay());  // Start the stop replay coroutine
            playReplayOnce = true;  // Set the playReplayOnce bool to true
        }
    }

    // Reset the timer to its initial value
    public void ResetTimer()
    {
        timer = 300.0f;  // Reset the timer value
        DisplayTime();  // Update the timer display
        StartTimer();  // Start the timer
    }

    // End the current period
    public void EndOfPeriod()
    {
        if (IsServer)
        {
            endOfPeriod.Value = true;  // Set the endOfPeriod network variable to true on the server
        }
        timerText.ToString();  // Convert the timer text to a string
        timerText.color = Color.red;  // Set the timer text color to red
        if (IsHost)
        {
            isTimer.Value = false;  // Set the isTimer network variable to false on the host
        }
        StartCoroutine(ShowEndOfPeriod());  // Start the show end of period coroutine
    }

    // Handle the timer countdown and check if the period has ended
    void HandleTimer()
    {
        if (isTimer.Value)
        {
            timer -= Time.deltaTime;  // Decrease the timer value based on deltaTime
            DisplayTime();  // Update the timer display
        }

        if (timer <= 0f)
        {
            timerText.text = string.Format("00:00");  // Set the timer text to "00:00"
            timerText2.text = string.Format("00:00");  // Set the secondary timer text to "00:00"
            EndOfPeriod();  // End the current period
        }
    }

    // Coroutine to start the replay
    public IEnumerator StartReplay()
    {
        Debug.Log("StartReplay");
        yield return new WaitForSeconds(3);  // Wait for 3 seconds
        PuckManager.Instance.DestroyAllPucks();  // Destroy all pucks in the PuckManager
        playReplay = true;  // Set the playReplay bool to true
        Debug.Log("Replay Started");
    }

    // Coroutine to stop the replay
    public IEnumerator StopReplay()
    {
        Debug.Log("StopReplay");
        yield return new WaitForSeconds(7);  // Wait for 7 seconds
        playReplay = false;  // Set the playReplay bool to false
        playReplayOnce = false;  // Reset the playReplayOnce bool
        Debug.Log("Replay Stopped");
    }

    // Coroutine to show the end of the period
    public IEnumerator ShowEndOfPeriod()
    {
        if (IsServer)
        {
            endOfPeriod.Value = true;  // Set the endOfPeriod network variable to true on the server
        }
        yield return new WaitForSeconds(3);  // Wait for 3 seconds
        playerCamera.gameObject.SetActive(false);  // Deactivate the player camera
    }
}
```

</details>

FPH (First Person Hockey) is a sports game developed using Unity and programmed in C#. The game offers both an online mode and a training mode. FPH falls under the sports genre with semi-realistic gameplay mechanics, providing an immersive first-person perspective.

Controls:
Movement: Players navigate the game environment using the WASD keys on the keyboard (support xbox controller).
Camera: The camera follows the player's head movements, simulating a first-person perspective. The mouse is used to control the camera direction and aim.
Actions: shooting and passing are performed using mouse clicks and keyboard inputs.

Game Modes:
Online Mode: Players can connect with opponents online.
Training Mode: Single player training.


</details>


<details>
<summary><h3>Title: Animal Game https://play.unity.com/mg/other/build-6ic</h3><img alt="AnimalGame" width="500px" src="https://raw.githubusercontent.com/Erto87/Erto87/main/AnimalGame.png"/></summary>

<details>
<summary><h3>Example code I made for sprinting</h3></summary>
  
```
public class PlayerSprintHandler : MonoBehaviour
{
    public Slider staminaBar;  // Reference to the UI slider that displays stamina

    private int maxStamina = 100;  // Maximum stamina value
    [HideInInspector] public int currentStamina;  // Current stamina value (hidden in the Inspector)

    private WaitForSeconds regenTick = new WaitForSeconds(0.1f);  // Wait time between stamina regeneration ticks
    private Coroutine regen;  // Coroutine for stamina regeneration

    public static PlayerSprintHandler instance;  // Static reference to the PlayerSprintHandler instance

    private void Awake()
    {
        instance = this;  // Set the static instance reference to this script
    }

    void Start()
    {
        currentStamina = maxStamina;  // Set the current stamina to the maximum value
        staminaBar.maxValue = maxStamina;  // Set the maximum value of the stamina bar UI
        staminaBar.value = maxStamina;  // Set the initial value of the stamina bar UI to maximum
    }

    public void UseStamina(int amount)
    {
        if (currentStamina - amount >= 0)  // Check if there's enough stamina to use
        {
            currentStamina -= amount;  // Decrease the current stamina by the specified amount
            staminaBar.value = currentStamina;  // Update the stamina bar UI

            if (regen != null)
                StopCoroutine(regen);  // Stop the stamina regeneration coroutine if it's already running

            regen = StartCoroutine(RegenStamina());  // Start the stamina regeneration coroutine
        }
        else
        {
            Debug.Log("OUT OF STAMINA");  // Output a debug message indicating that there's not enough stamina
        }
    }

    private IEnumerator RegenStamina()
    {
        yield return new WaitForSeconds(2);  // Wait for 2 seconds before starting the stamina regeneration

        while (currentStamina < maxStamina)  // Continue the loop until stamina reaches the maximum value
        {
            currentStamina += maxStamina / 100;  // Increment the stamina by a percentage of the maximum value
            staminaBar.value = currentStamina;  // Update the stamina bar UI
            yield return regenTick;  // Wait for the specified time before the next regeneration tick
        }

        regen = null;  // Set the stamina regeneration coroutine reference to null when regeneration is complete
    }
}
```

</details>



What is it?
The project is a 2D top-down action game played as an animal, showcasing the life of the animal with information about it. It will be a browser game.

Gameplay mechanics
The player chooses a continent with an animal on the world map. After that, the player gets to play as the animal in a top-down view, gathering food and avoiding dangers until reaching the finish line.

The maps are freeroam areas.

The player will find information "pop-ups" in the form of signs, providing details about the playable animal, its environment, and the threats it faces. At the end of each level, there will be a quiz based on the information pop-ups. Points are awarded for correct quiz answers.

The quiz will be located at the center of the map and won't allow the player to answer it if they haven't found all the information pop-ups. It will notify how many are still missing.

The player moves by WASD.

Enemies will patrol the level, attacking the player if they get close enough.

Food pickups heal the player.
Art style
The art style will be cartoon-like, with large colorful assets to appeal to children. The character art will be in a top-down perspective, while the environmental art will be partially top-down and partially side view.

</details>

<details>
<summary><h3>Title: Delirium https://play.unity.com/mg/other/deliriumbuild</h3><img alt="Delirium" width="500px" src="https://raw.githubusercontent.com/Erto87/Erto87/main/Delirium.png"/></summary>

Delirium is inspired by Limbo and is a 2D platformer where the player's goal is to survive and reach end of the level. Player controls a small character in a dark and eerie environment and must collect glowing orbs. However, the journey is not easy as there are two different types of enemies that attempt to impede the player's progress.
</details>
  
<h2>🧰 Tools</h2>
<div style="display:flex;">
  <div style="margin-right:20px;">
    <img alt="Unity" width="100px" src="https://upload.wikimedia.org/wikipedia/commons/8/8a/Official_unity_logo.png"/>
  </div>
  <div>
    <img alt="C#" width="100px" src="https://upload.wikimedia.org/wikipedia/commons/4/4f/Csharp_Logo.png"/>
  </div>
</div>
<br />

<h2>🤳 Connect with me:</h2>
<a href="https://www.linkedin.com/in/tapio-kettunen-aa46a21b4" target="_blank">Visit my LinkedIn profile</a>



<!--
**Erto87/Erto87** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
