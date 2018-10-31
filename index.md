## Pacman Clon

A continuación pueden copiar o descargar los archivos necesarios para el Taller de Videojuegos - Pacman para móviles

### Imágenes
Icono de Pacman
![Icono de Pacman](https://FlorBergesio.github.io/Sprites/pacman-icon.png)

Sprite de Pacman
![Sprite de Pacman](https://FlorBergesio.github.io/Sprites/pacman.png)

Sprite de Laberinto
![Sprite de Laberinto](https://FlorBergesio.github.io/Sprites/maze.png)

Sprite de Tile Map
![Sprite de Tile Map](https://FlorBergesio.github.io/Sprites/TileMap.png)

Sprite de Comida o Pac-dots
![Sprite de Comida o Pac-dots](https://FlorBergesio.github.io/Sprites/food.png)

Sprites de Fantasmas y otros
![Sprite de Fantasma Blinky](https://FlorBergesio.github.io/Sprites/blinky.png)
![Sprite de Fantasma Inky](https://FlorBergesio.github.io/Sprites/inky.png)
![Sprite de Fantasma Pinky](https://FlorBergesio.github.io/Sprites/pinky.png)
![Sprite de Fantasma Clyde](https://FlorBergesio.github.io/Sprites/clyde.png)
![Sprite miscelaneos](https://FlorBergesio.github.io/Sprites/others.png)

### SFX
[Link](https://github.com/FlorBergesio/FlorBergesio.github.io/tree/master/SFX)

### Códigos

PacmanBehaviour.cs

```markdown
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityStandardAssets.CrossPlatformInput;

public class PacmanBehaviour : MonoBehaviour {
	public float speed = 0.2f;
	float dirX,dirY;
	Vector2 destination = Vector2.zero;
	public int foodQuantity;
	public SceneChanger sceneChanger;

	void Start () {
		destination = transform.position;
		foodQuantity = GameObject.FindGameObjectsWithTag ("Food").Length;
	}

	void Update () {
		dirX = CrossPlatformInputManager.GetAxis ("Horizontal");
		if (Input.GetKey ("left") || Input.GetKey ("right"))
			dirX = Input.GetAxis ("Horizontal");
		dirY = CrossPlatformInputManager.GetAxis ("Vertical");
		if (Input.GetKey ("up") || Input.GetKey ("down"))
			dirY = Input.GetAxis ("Vertical");
	}

	void FixedUpdate(){
		Vector2 p = Vector2.MoveTowards (transform.position, destination, speed);
		GetComponent<Rigidbody2D> ().MovePosition (p);

		if (dirX > 0.1 && validMovement (Vector2.right)) 
			destination = (Vector2)transform.position + Vector2.right;
		if (dirX < -0.1 && validMovement (-Vector2.right)) 
			destination = (Vector2)transform.position - Vector2.right;
		if (dirY > 0.1 && validMovement (Vector2.up)) 
			destination = (Vector2)transform.position + Vector2.up;
		if (dirY < -0.1 && validMovement (-Vector2.up)) 
			destination = (Vector2)transform.position - Vector2.up;

		Vector2 direction = destination - (Vector2)transform.position;
		GetComponent<Animator> ().SetFloat ("DirX", direction.x);
		GetComponent<Animator> ().SetFloat ("DirY", direction.y);
	}

	bool validMovement (Vector2 dir) {
		Vector2 pos = transform.position;
		RaycastHit2D hit = Physics2D.Linecast (pos + dir, pos);
		return (hit.collider == GetComponent<Collider2D> ());
	}

	void OnTriggerEnter2D(Collider2D other){
		if (other.tag == "Food") {
			foodQuantity--;
			if (foodQuantity ==0)
				sceneChanger.ChangeSceneTo ("PacmanWinScene");
		}
	}
}
```

FoodBehaviour.cs

```markdown
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class FoodBehaviour : MonoBehaviour {
	public static int scorePoints;
	public Text scoreText;

	void Start() {
		scorePoints = 0; 
	}

	void OnTriggerEnter2D(Collider2D other){
		if (other.tag == "Player") {
			Destroy (gameObject);
			scorePoints++;
			scoreText.text = "Score: " + scorePoints.ToString ();
		}
	}
}
```

GhostBehaviour.cs

```markdown
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GhostBehaviour : MonoBehaviour {
	public Transform[] waypoints;
	int current = 0;
	public float speed = 0.15f;
	public SceneChanger sceneChanger;

	void FixedUpdate () {
		if (transform.position != waypoints [current].position) {
			Vector2 p = Vector2.MoveTowards (transform.position, waypoints [current].position, speed);
			GetComponent<Rigidbody2D> ().MovePosition (p);
			GetComponent<Animator> ().SetFloat ("DirX", p.x);
			GetComponent<Animator> ().SetFloat ("DirY", p.y);
		} else
			current = (current + 1) % waypoints.Length;
	}

	void OnTriggerEnter2D(Collider2D other){
		if (other.tag == "Player") {
			Destroy (other.gameObject);
			sceneChanger.ChangeSceneTo ("PacmanGameOver");
		}
	}
}
```

SceneChanger.cs

```markdown
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneChanger : MonoBehaviour {

	public void ChangeSceneTo(string sceneName){
		SceneManager.LoadScene (sceneName);
	}
}
```
