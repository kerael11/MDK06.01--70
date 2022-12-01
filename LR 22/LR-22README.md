Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: разработка игры Make Them Fall

1. Импортирование ресурсов в проект.
Создаю папки Scripts, Sprites и импортирую ресурсы для создания игры.
Используя редактор срезов получаю несколько спрайтов игрока.
 [![Screenshot-7.png](https://i.postimg.cc/htxkJK7m/Screenshot-7.png)](https://postimg.cc/DSvjHk6f)
Рисунок 1 
2. Размещение игрока и фона на сцене.
Создаю объект Player и к нему объект Sprite, добавляю спрайт самого игрока 
 [![Screenshot-8.png](https://i.postimg.cc/TwkF8wBz/Screenshot-8.png)](https://postimg.cc/zbg0KJz7)
Рисунок 2 
 [![Screenshot-9.png](https://i.postimg.cc/Prvccdy7/Screenshot-9.png)](https://postimg.cc/fJZBSn67)
Рисунок 3 
[![Screenshot-10.png](https://i.postimg.cc/YCcVBTyC/Screenshot-10.png)](https://postimg.cc/jLXvz3bp)
 
Рисунок 4 

3. Скрипт Player
Создание скрипта передвижения игроков.
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 xValue;
    public bool changeDirecionBool, leftFacing, changeIt, easing;

    Animator animator;
    Vector3 desiredPosition;

    void Start()
    {
        DesiredPosition();
        ChangeDirection();
        animator = GetComponent<Animator>();
    }   


    void Update()
    {
        if (changeIt)
        {
            if (easing)
            {
                transform.position = Vector3.Lerp(transform.position, desiredPosition, .1f);
            }
            else
            {
                transform.position = desiredPosition;
            }

            transform.position = Vector3.Lerp(transform.position, desiredPosition, .1f);

            if (Mathf.Abs(desiredPosition.x - transform.position.x) <= 0.1f)
            {
                Debug.Log("Changing gavity");
                changeIt = false;
            }
        }
        animator.SetBool("ChangeDirection", changeDirecionBool);


    }

    public void CallThisMethod()
    {
        DesiredPosition();
        ChangeDirection();

    }

    public void DesiredPosition()
    {
        desiredPosition = new Vector2(changeDirecionBool ? xValue.x : xValue.y, transform.position.y);
    }

    void ChangeDirection()
    {
        changeDirecionBool = !changeDirecionBool;
        // transform.position = Vector3.Lerp(transform.position, desiredPosition,.1f);
        if(changeDirecionBool != leftFacing)
        {
            changeIt = true;

            leftFacing = changeDirecionBool;
        }

        Debug.Log(transform.position.x);
    }

    public void OnTriggerEnter2D (Collider2D collision)
    {
        if (collision.tag == "Spike")
        {
            Debug.Log("dead Player");
            FindObjectOfType<GameManager>().RestartScene();
        }
    }
}

4. Скрипт Spike
Создание скрипта передвижения препятствий. 
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spike : MonoBehaviour
{
    public float moveSpeed;
    public Vector2 minMaxValue;
    Vector2 pos;

    void Start()
    {
        
    }

    void Update()
    {
        //Vector3.Up = new Vector3(0,1,0)
        transform.position += Vector3.up * moveSpeed * Time.deltaTime;

        if (transform.position.y > minMaxValue.x)
        {
            pos = transform.position;
            pos.x = transform.position.x;
            pos.y = minMaxValue.y;
            transform.position = pos;
            ShowOrHide();
        }
    }

    void ShowOrHide()
    {
        if (Random.value > 0.5f)
        {
            Debug.Log("Disabling");
            transform.GetChild(0).gameObject.SetActive(false);
            return;
        }

        transform.GetChild(0).gameObject.SetActive(true);

    }
}

5. Скрипт  Collider и ClickChecke
Создание скрипта смены анимации игрока при переходе на другую сторону в игре.

Collider:

using UnityEngine;

public class Collider : MonoBehaviour
{
    public Transform PlayerTransform;

    public void CallPlayerMethod()
    {
        Debug.Log(PlayerTransform.name);
        PlayerTransform.GetComponent<Player>().CallThisMethod();
    }

}

ClickChecker:

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ClickChecker : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Vector2 pos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
            RaycastHit2D hit = Physics2D.Raycast(pos, Vector2.zero);
            if(hit)
            {
                if (hit.transform.name == "LeftSide")
                {
                    hit.collider.GetComponent<Collider>().CallPlayerMethod();
                }
                else if (hit.transform.name == "RightSide")
                {

                    hit.collider.GetComponent<Collider>().CallPlayerMethod();
                }
                else
                {
                    Debug.Log("Unkown hit from click Checker");
                    return;
                }
            }
        }
    }
}

 [![Screenshot-11.png](https://i.postimg.cc/L51czS0G/Screenshot-11.png)](https://postimg.cc/RJvpBkmT)
Рисунок 5
