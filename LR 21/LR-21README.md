Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: разработка игры 2 CARS

1.Визуализация аспектов игры
  [![Screenshot-3.png](https://i.postimg.cc/rmY45N2D/Screenshot-3.png)](https://postimg.cc/S2Cjh8s4)
Рисунок 1

2. Скрипт Car
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Car : MonoBehaviour
{
    public bool FirstLaneBlueCar, FirstLaneOrangeCar;
    public bool BlueCar;
    public Vector2 Xpos;




    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        if(Input.GetKeyDown("left"))
        {
            LeftButtonPressed();
        }
        if (Input.GetKeyDown("right"))
        {
            RightButtonPressed();
        }
        if(BlueCar)
        {
            if(FirstLaneBlueCar)
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(-Xpos.y, transform.position.y, 0), .1f);
            }
            else
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(-Xpos.x, transform.position.y, 0), .1f);
            }
        }
        else
        {
            if(FirstLaneOrangeCar)
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(Xpos.y, transform.position.y, 0), .1f);
            }
            else
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(Xpos.x, transform.position.y, 0), .1f);
            }
        }
    }
    public void LeftButtonPressed()
    {
        if (FirstLaneBlueCar) { FirstLaneBlueCar = false; } else { FirstLaneBlueCar = true; }
    }
    public void RightButtonPressed()
    {
        if (FirstLaneOrangeCar) { FirstLaneOrangeCar = false; } else { FirstLaneOrangeCar = true; }
    }

    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "circle")
        {
            //give points
            FindObjectOfType<GameController>().AddScore();
            Destroy(collision.gameObject);
        }
        if(collision.tag == "Cube")
        {
            FindObjectOfType<GameController>().GameOver();
            Debug.Log("playerGO");
        }
    }
}

3. Скрипт CircleSquareGo
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CircleSquareGo : MonoBehaviour
{
    int speed;
    Rigidbody2D rgbd;
    void Start()
    {
        speed = 10;
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.velocity = new Vector2(0, -speed);
    }

    
}



 4. Скрипт EdgeCollider
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EdgeCollider : MonoBehaviour
{

    public void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.tag == "circle")
        {
            //gameOver
            Destroy(collision.gameObject);
        }
        else if(collision.tag == "Cube")
        {
            //destroy go
            Destroy(collision.gameObject);
        }
    }
}



5. Скрипт GameController
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class GameController : MonoBehaviour
{
    //Circle And Square GO
    public GameObject[] BlueGO;
    public GameObject[] OrangeGO;
    // To instantiate squate and circle
    public float startWait;
    public float spawnWait;

    GameObject Blue, Orange;

    float[] XPosition = new float[2] { 1.5f, 4.6f };
    bool GameOverBool;
    int Score;
    public Text ScoreText;
    public GameObject GameOverCanvas;
    void Start()
    {
        GameOverBool = false;
        Time.timeScale = 1;
        GameOverCanvas.SetActive(false);

    }

    // Update is called once per frame
    void Update()
    {

    }
    IEnumerator SpawnObjects()
    {
        yield return new WaitForSeconds(startWait);
        while(true)
        {
            for ( int i = 0; i< 50; i++)
            {
                float OrangeXpos = XPosition[Random.Range(0, XPosition.Length)];

                Vector3 OrangePos = new Vector3(OrangeXpos, 10, 0);

                Orange = OrangeGO[Random.Range(0, OrangeGO.Length)] as GameObject;

                Instantiate(Orange, OrangePos, Quaternion.identity);
                yield return new WaitForSeconds(spawnWait);

                float BlueXpos = -XPosition[Random.Range(0, XPosition.Length)];

                Vector3 BluePos = new Vector3(BlueXpos, 10, 0);
                Blue = BlueGO[Random.Range(0, BlueGO.Length)] as GameObject;
                Instantiate(Blue, BluePos, Quaternion.identity);
                yield return new WaitForSeconds(spawnWait);

            }
        }
    }
    public void StartGame()
    {
        StartCoroutine(SpawnObjects());
    }
    public void GameOver()
    {
        GameOverBool = true;
        Time.timeScale = 0;
        GameOverCanvas.SetActive(true);
    }
    public void AddScore()
    {
        Score += 1;
        ScoreText.text = Score.ToString();
    }
    public void Restart()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}
