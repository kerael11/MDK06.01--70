Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: Разработка игры симулятора фермы

⦁	Экспортим подготовленные спрайты одной картинкой в наш созданный проект.
 
Рисунок 1 
![рис1](https://user-images.githubusercontent.com/119409903/205114207-86dd6060-aedc-4b72-8ebc-57376245d1f2.png)

⦁	После спрайтов приступим к созданию первого и самого главного префаба (Plot)
 
Рисунок 2
![Screenshot_6](https://user-images.githubusercontent.com/119409903/205114283-45a70b8a-3771-4bd5-bc19-c1d633fcc908.png)

⦁	Скрипт Plot Manager
Для начала добавим на него Polygon Collider2D и Rigidbody2D, После напишем скрипт на взаимодействие с растениями 
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlotManager : MonoBehaviour
{

    bool isPlanted = false;
    SpriteRenderer plant;
    BoxCollider2D plantCollider;

    int plantStage = 0;
    float timer;

    public Color availableColor = Color.green;
    public Color unavailableColor = Color.red;

    SpriteRenderer plot;

    PlantObject selectedPlant;

    FarmManager fm;

    bool isDry = true;
    public Sprite drySprite;
    public Sprite normalSprite;
    public Sprite unavailableSprite;


    float speed=1f;
    public bool isBought=true;


    

    
    void  Start()
    {
        plant = transform.GetChild(0).GetComponent<SpriteRenderer>();
        plantCollider = transform.GetChild(0).GetComponent<BoxCollider2D>();
        fm = transform.parent.GetComponent<FarmManager>();
        plot = GetComponent<SpriteRenderer>();
        if(isBought)
        {
            plot.sprite = drySprite; 
        }
        else
        {
            plot.sprite = unavailableSprite;
        }
        
    }

    
    void Update()
    {
        if (isPlanted && !isDry) 
        {
            timer -= speed*Time.deltaTime;

            if (timer < 0 && plantStage<selectedPlant.plantStages.Length-1)
            {
                timer = selectedPlant.timeBtwStages;
                plantStage++;
                UpdatePlant();
            }
        }
    }

    private void OnMouseDown()
    {
        if (isPlanted)
        {
            if(plantStage == selectedPlant.plantStages.Length - 1 && fm.isPlanting && !fm.isSelecting)
            {
                Harvest();
            }
           

        }
        else if(fm.isPlanting && fm.selectPlant.plant.buyPrice <= fm.money && isBought)
        {
            Plant(fm.selectPlant.plant);
        }
        if (fm.isSelecting)
        {
            switch(fm.selectedTool)
            {
                case 1:
                    if (isBought)
                    {
                        isDry = false;
                        plot.sprite = normalSprite;
                        if (isPlanted) UpdatePlant();
                    }
                    break;
                case 2:
                    if (fm.money>=10 && isBought )
                    {
                        fm.Transaction(-10);
                        if (speed < 2) speed += .2f;
                    }
                    break;
                case 3:
                    if (fm.money >= 100 && !isBought)
                        isBought = true;
                    plot.sprite = drySprite;
                    {
                        fm.Transaction(-100);
                        isBought = true;
                        plot.sprite = drySprite;
                    }
                    break;

                default:
                    break;
            }
        }   
    }







⦁	Далее настроили возможность построить из наших блоков земли всевозможные плантации и формы по соображению игроков. Также написали скрипт на изометрию блоков.
 
Рисунок 3 
![Screenshot_1](https://user-images.githubusercontent.com/119409903/205114371-3cc7962c-5be5-4762-bbba-5cb37ad63762.png)

⦁	Скрипт Palette
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class IsometricZ : MonoBehaviour
{
    
    void Start()
    {
        transform.position = new Vector3(transform.position.x, transform.position.y, transform.position.y / 100);
    }
}
⦁	После приступили к созданию дальнейшего интерфейса и магазина.

 Рисунок 4
 ![Screenshot_2](https://user-images.githubusercontent.com/119409903/205114400-c99b0a0e-6bd3-4c53-8264-fbee3a87441e.png)

⦁	Скрипт StoreManager
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class StoreManager : MonoBehaviour
{
    public GameObject plantItem;
    List<PlantObject> plantObjects = new List<PlantObject>();

    private void Awake()
    {
        
        var loadPlants = Resources.LoadAll("Plants", typeof(PlantObject));
        foreach (var plant in loadPlants)
        {
            plantObjects.Add((PlantObject)plant);
        }
        plantObjects.Sort(SortByPrice);

        foreach (var plant in plantObjects)
        {
            PlantItem newPlant = Instantiate(plantItem, transform).GetComponent<PlantItem>();
            newPlant.plant = plant; 
        }
    }


    int SortByPrice(PlantObject plantObject1, PlantObject plantObject2)
    {
        return plantObject1.buyPrice.CompareTo(plantObject2.buyPrice);
    }

    int SortByTime(PlantObject plantObject1, PlantObject plantObject2)
   {
        return plantObject1.timeBtwStages.CompareTo(plantObject2.timeBtwStages);
    }
   }

⦁	Скрипт Farm Manager
Теперь напишем скрипт для посадки, покупки, полива и других нужных функций к нашей игре.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class FarmManager : MonoBehaviour
{
    public PlantItem selectPlant;
    public bool isPlanting = false;
    public int money=100;
    public Text moneyTxt;

    public Color buyColor = Color.green;
    public Color cancelColor = Color.red;

    public bool isSelecting = false;
    public int selectedTool=0;
    

    public Image[] buttonsImg;
    public Sprite normalButton;
    public Sprite selectedButton;

    
    void Start()
    {
        moneyTxt.text = "$" + money;
   }


    public void SelectPlant(PlantItem newPlant)
    {
        if(selectPlant == newPlant)
        {

            CheckSelection();
            
        }
        else
        {
            CheckSelection();
            selectPlant = newPlant;
            selectPlant.btnImage.color = cancelColor;
            selectPlant.btnTxt.text = "Cancel";
            isPlanting = true;
        }


    }

    public void SelectTool(int toolNumber)
    {
        if(toolNumber == selectedTool)
        {
         
           CheckSelection();
        }
       else
       {
           
            CheckSelection();
            isSelecting = true;
            selectedTool = toolNumber;
            buttonsImg[toolNumber - 1].sprite = selectedButton; 
        }
    }

    void CheckSelection()
    {
        if (isPlanting)
        {
            isPlanting = false;
            if (selectPlant != null)
            {
                selectPlant.btnImage.color = buyColor;
                selectPlant.btnTxt.text = "Buy";
                selectPlant = null;
            }
        }
        if(isSelecting)
        {
            if(selectedTool>0)
            {
                buttonsImg[selectedTool - 1].sprite = normalButton;
            }
            isSelecting = false;
            selectedTool = 0;
        }
    }

    public void Transaction(int value)
    {
        money += value;
        moneyTxt.text = "$" + money;

    }
}

⦁	Сохраняем игру в exe
 ![Screenshot_3](https://user-images.githubusercontent.com/119409903/205114425-d25a8fb4-d056-422d-8ebe-d80848aaa2bd.png)

Рисунок 5
