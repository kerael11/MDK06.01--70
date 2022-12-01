Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: Разработка игры Snake 

1.Настройка объектов
# ![Screenshot-1.png](https://i.postimg.cc/tC6qzNGL/Screenshot-1.png)
    Рисунок 18.1Настройка объектов
    
# ![Screenshot-2.png](https://i.postimg.cc/KcwcLswP/Screenshot-2.png)
    Рисунок 18.2
    
# 2. Скрипт Food

using UnityEngine

public class Food : MonoBehaviour
{

    public BoxCollider2D gridArea;

    private void Start()
    {
        RandomizePosition();
    }

    private void RandomizePosition()
    {
        Bounds bounds = this.gridArea.bounds;

        float x = Random.Range(bounds.min.x, bounds.max.x);
        float y = Random.Range(bounds.min.y, bounds.max.y);

        this.transform.position = new Vector3(Mathf.Round(x), Mathf.Round(y), 0.0f);
    }

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.tag == "Player")
        {
            RandomizePosition();
        }
    }
}
# 3. Скрипт Snake
using System.Collections;

using System.Collections.Generic;

using UnityEngine;

public class Snake : MonoBehaviour
{

    private Vector2 _direction = Vector2.right;

    private List<Transform> _segments = new List<Transform>();

    public Transform segmentPrefab;

    public int initialSize = 4;
    private void Start()
    {
        ResetState();
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.W))
        {
            _direction = Vector2.up;
        }
        else if (Input.GetKeyDown(KeyCode.S))
        {
            _direction = Vector2.down;
        }
        else if (Input.GetKeyDown(KeyCode.A))
        {
            _direction = Vector2.left;
        }
        else if (Input.GetKeyDown(KeyCode.D))
        {
            _direction = Vector2.right;
        }
    }
    private void FixedUpdate()
    {
        for (int i = _segments.Count - 1; i > 0; i--)
        {
            _segments[i].position = _segments[i - 1].position;
        }

       this.transform.position = new Vector3(
           Mathf.Round(this.transform.position.x) + _direction.x,
           Mathf.Round(this.transform.position.y) + _direction.y,
           0.0f);
    }

    private void Grow()
    {
        Transform segment = Instantiate(this.segmentPrefab);
        segment.position = _segments[_segments.Count - 1].position;

        _segments.Add(segment);
    }
    private void ResetState()
    {
        for (int i = 1; i < _segments.Count; i++)
        {
            Destroy(_segments[i].gameObject);
        }

        _segments.Clear();
        _segments.Add(this.transform);

        for (int i = 1; i < this.initialSize; i++)
        {
            _segments.Add(Instantiate(this.segmentPrefab));
        }

        this.transform.position = Vector3.zero;
    }

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.tag == "Food")
        {
            Grow();
        }
        else if (other.tag == "Obstacle")
        {
            ResetState();
        }
    }
}


