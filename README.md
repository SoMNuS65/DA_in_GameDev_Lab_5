# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнил(а):
- Шайхутдинов
- РИ-220931

Отметка о выполнении заданий:

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
Ход работы:
### Найдите внутри C# скрипта “коэффициент корреляции ” и сделать выводы о том, как он влияет на обучение модели.

С#-скрипт, в котором находим коэффициент корреляции расположен ниже, метод OnActionReceived() - имеет искомый коэффициент. Задача агента - найти объект, он высчитывает расстояние, на котором находится тот или иной объект. Чем выше коэффициент корреляции, тем легче определить местонахождение объекта.

```C#

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
  Rigidbody rBody;
  // Start is called before the first frame update
  void Start()
  {
    rBody = GetComponent<Rigidbody>();
  }

  public Transform Target;
  public override void OnEpisodeBegin()
  {
    if (this.transform.localPosition.y < 0)
    {
    this.rBody.angularVelocity = Vector3.zero;
    this.rBody.velocity = Vector3.zero;
    this.transform.localPosition = new Vector3(0, 0.5f, 0);
    }

  Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
  }
    public override void CollectObservations(VectorSensor sensor)
  {
    sensor.AddObservation(Target.localPosition);
    sensor.AddObservation(this.transform.localPosition);
    sensor.AddObservation(rBody.velocity.x);
    sensor.AddObservation(rBody.velocity.z);
  }
  public float forceMultiplier = 10;

  public override void OnActionReceived(ActionBuffers actionBuffers)
  {
    Vector3 controlSignal = Vector3.zero;
    controlSignal.x = actionBuffers.ContinuousActions[0];
    controlSignal.z = actionBuffers.ContinuousActions[1];
    rBody.AddForce(controlSignal * forceMultiplier);

    float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

    if(distanceToTarget < 1.42f)
    {
      SetReward(1.0f);
      EndEpisode();
    }
    else if (this.transform.localPosition.y < 0)
    {
      EndEpisode();
    }
  }
}

```
## Задание 2
-

## Задание 3
-

## Powered by
Шайхутдинов Р.
