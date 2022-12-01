
Выполнил: Комлев Д.А.

Группа: ЭВТ-70

Игровой движок: Unity 2022.1.23

Название работы: Разработка проекта Timer

⦁	Выполнение работы

Было создано несколько моделей разных таймеров.
 
Рисунок 1
![image](https://user-images.githubusercontent.com/119409903/205133743-195ef532-4c8f-4a81-a865-c680d98ead94.png)

⦁	Скрипт Timer
Код функционирования таймера.
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.Events;

public class Timer : MonoBehaviour
{
    [Header("Timer UI references :")]
    [SerializeField] private Image uiFillImage;
    [SerializeField] private Text uiText;

    public int Duration { get; private set; }

    public bool IsPaused { get; private set; }

    private int remainingDuration;

    private UnityAction onTimerBeginAction;
    private UnityAction<int> onTimerChangeAction;
    private UnityAction onTimerEndAction;
    private UnityAction<bool> onTimerPauseAction;

    private void Awake()
    {
        ResetTimer();
    }

    private void ResetTimer()
    {
        uiText.text = "00:00";
        uiFillImage.fillAmount = 0f;

        Duration = remainingDuration = 0;

        onTimerBeginAction = null;
        onTimerChangeAction = null;
        onTimerEndAction = null;
        onTimerPauseAction = null;

        IsPaused = false;
    }

    public void SetPaused (bool paused)
    {
        IsPaused = paused;

        if (onTimerPauseAction != null)
            onTimerPauseAction.Invoke(IsPaused);
    }

    public Timer SetDuration(int seconds)
    {
        Duration = remainingDuration = seconds;
        return this;
    }

    public Timer OnBegin (UnityAction action)
    {
        onTimerBeginAction = action;
        return this;
    }
    public Timer OnChange(UnityAction<int> action)
    {
        onTimerChangeAction = action;
        return this;
    }
    public Timer OnEnd(UnityAction action)
    {
        onTimerEndAction = action;
        return this;
    }
    public Timer OnPause(UnityAction<bool> action)
    {
        onTimerPauseAction = action;
        return this;
    }


    public void Begin()
    {
        if (onTimerBeginAction != null)
            onTimerBeginAction.Invoke();

        StopAllCoroutines();
        StartCoroutine(UpdateTimer());
    }

    private IEnumerator UpdateTimer ()
    {
        while (remainingDuration > 0)
        {
            if (!IsPaused)
            {
                if (onTimerChangeAction != null)
                    onTimerChangeAction.Invoke(remainingDuration);

                UpdateUI(remainingDuration);
                remainingDuration--;
            }
            yield return new WaitForSeconds(1f);
        }
        End();
    }

    private void UpdateUI (int seconds)
    {
        uiText.text = string.Format("{0:D2}:{1:D2}", seconds / 60, seconds % 60);
        uiFillImage.fillAmount = Mathf.InverseLerp(0, Duration, seconds);
    }

    public void End ()
    {
        if (onTimerEndAction != null)
            onTimerEndAction.Invoke();

        ResetTimer();
    }

    private void OnDestroy()
    {
        StopAllCoroutines();
    }
}

⦁	Скрипт Demo
Скрипт для управления таймера.
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Demo : MonoBehaviour
{
    [SerializeField] Timer timer1;
    [SerializeField] Timer timer2;
    [SerializeField] Timer timer3;
    [SerializeField] Timer timer4;

    private void Start()
    {
        timer1
        .SetDuration(5)
        .OnEnd (() => Debug.Log ("Timer1 ended"))
        .Begin();

        timer2
        .SetDuration(10)
        .OnEnd(() => Debug.Log("Timer2 ended"))
        .Begin();

        timer3
        .SetDuration(15)
        .OnEnd(() => Debug.Log("Timer3 ended"))
        .Begin();

        timer4
        .SetDuration(20)
        .OnEnd(() => Debug.Log("Timer4 ended"))
        .Begin();
    }
}
