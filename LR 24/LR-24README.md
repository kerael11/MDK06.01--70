Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: разработка игры Circle

1.	Добавляем спрайты
Корректируем спрайт Круга чтобы он был полноценным, после добавляем спрайт линии.
 
 [![Screenshot-12.png](https://i.postimg.cc/KYQ32SQX/Screenshot-12.png)](https://postimg.cc/VSrkqh34)
 
Рисунок 1

2.	Пишем скрипт на Player
В этом скрипте делаем так чтобы после каждого нажатия ЛКМ круг отталкивался и так же при нажатии ЛКМ добавлялось в счетчик кликов
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 jumpForce;
    Vector2 currentVelocity;
    Rigidbody2D rgbd;
    GameManager gameManager;
    ScoreUI scoreUI;

    void Start()
    {
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.gravityScale = 0;
        gameManager = FindObjectOfType<GameManager>();
        scoreUI = FindObjectOfType<ScoreUI>();
    }

    void Update()
    {
        if (gameManager.gameOver) { rgbd.bodyType = RigidbodyType2D.Static;return; }
        if (Input.GetMouseButtonDown(0))
        {
            if (rgbd.gravityScale != 0.5f) { rgbd.gravityScale = 0.5f; }
            rgbd.AddForce(jumpForce);
            SpeedController();
            scoreUI.IncrementScore(1);
        }
    }

    void SpeedController()
    {
        currentVelocity = rgbd.velocity;
        currentVelocity.x = Mathf.Clamp(currentVelocity.x, 2, 2);
        currentVelocity.y = Mathf.Clamp(currentVelocity.y, 2, 2);
        rgbd.velocity = currentVelocity;
    }
}


3.	Пишем скрипт на CameraController
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{
    public Transform playerTransform;
    void Start()
    {
        
    }

    void Update()
    {
        transform.position = new Vector3(playerTransform.position.x, transform.position.y, -10);
    }
}


4.	Раставляем линии и делаем коллайдеры на Circle
Раставили линии в произвольном порядке и ставим сверху и снизу круга Box Collider и если эти коллайдеры докоснутся до линии, то игра закончится
 
 [![Screenshot-13.png](https://i.postimg.cc/K8DDRWQf/Screenshot-13.png)](https://postimg.cc/0zrw4cTK)
 
Рисунок 2

5.	Пишем скрипт на линии
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Block : MonoBehaviour
{
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.tag == "Player")
        {
            Debug.Log("hit by player");
            FindObjectOfType<GameManager>().gameOver = true;
        }
    }
}

6.	Пишем скрипт на Алмазы 
Делаем так чтобы при косании аламазы собирались и добавлялись к счетчику алмазов
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Diamond : MonoBehaviour
{
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.tag == "Player")
        {
            //add points
            FindObjectOfType<ScoreUI>().IncrementDiamond(1);
            FindObjectOfType<ScorePointCanvas>().DiamondHit(collision.transform.position);
            Destroy(gameObject);
        }
    }
}
7.	Пишем скрипт на счет алмазов и кликов
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ScoreUI : MonoBehaviour
{
    int Score,Diamond;
    public TMPro.TextMeshProUGUI ScoreText;
    public TMPro.TextMeshProUGUI DiamondText;

    public void IncrementScore( int value)
    {
        Score += value;
        UpdateDisplay();
    }

    public void IncrementDiamond(int value)
    {
        Diamond += value;
        UpdateDisplay();
    }

    private void UpdateDisplay()
    {
        ScoreText.text = Score.ToString();
        DiamondText.text = Diamond.ToString();
    }
}
 
 [![Screenshot-14.png](https://i.postimg.cc/FHp09Lsn/Screenshot-14.png)](https://postimg.cc/xXkX5qmK)
 
Рисунок 3





