# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнил(а):
- Абросимова Марина Михайловна
- РИ-220931

Отметка о выполнении заданий:

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
Ход работы:
- Найдите внутри C# скрипта “коэффициент корреляции ” и сделать выводы о том, как он влияет на обучение модели.

Скрипт, в котором находим коэффициент корреляции:

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

Метод OnActionReceived содержит искомый коэффициент:

```C#

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

```

Агент должен обнаружить обьект и для этого используеться данный коэффициент кореляции. Он определяет расстояние, на котором находиться искомый обьект. Чем больше коэффициент, тем проще будет найти объект..

## Задание 2
Ход работы:
- Изменить параметры файла yaml-агента и определить какие параметры и как влияют на обучение модели. Привести описание не менее трех параметров.

файл yaml-агента
![2023-12-05_17-25-06](https://github.com/Marishka-A/Workshop5/assets/126682278/eb603907-1af9-47b7-bb82-a5d6aed23b94)

Из него я выбрала 4 параметра, которые опишу:

- lambd:

Лямбда-параметр, который используется для подсчета обновленной оценки преимущества. Тоесть это коэффициент, позволяющий рассчитать на что полагается агент в процессе обучения.

При изменении: Значения ниже нормы обеспечивают полагание на текущую оценку, а выше нормы на будущее вознаграждение.

- gamma:

Коэффициент дисконтирования для будущих вознаграждений из среды. Показывает возможность получения агентом будущего вознаграждения. Не может быть >= 1.

При изменении: При малом числе агент получает вознаграждение раньше, так как это более необходимо. При большом агент получит вознаграждение немного позже так как это не так необходимо.

- num_epoch:

Считает количество обрабатываемых данных обучения моделью в каждой эпохе.

При изменении: Чем больше параметр - тем точне обучение. Но из-за этого увеличивается время, за которое проходит это обучение.

- max_steps:

Общее количество шагов и действий при обучении, которые должны быть выполнены в среде до завершения процесса.

При изменении: При нескольких агентов с одинаковым именем, все шаги, выполняемые этими агентами, будут способствовать одинаковому количеству max_steps.

## Задание 3
Ход работы:
- Приведите примеры, для каких игровых задачи и ситуаций могут использоваться примеры 1 и 2 с ML-Agent’ом(1). В каких случаях проще использовать ML-агент, а не писать программную реализацию решения?(2)


1) Примеры 1 и 2 ML-агента можно использовать при создании механик для любого игрового босса. Агента можно научить следовать за игроком, чтобы наносить ему урон и возвращаться на базу при низком здоровье для лечения. Также его можно использовать при условии, что с игроком ходят мобы/миньены (научить их двигаться строго по траектории игорока и не теряться, не застревать в текстурах).

2) Использование ML-агента не всегда хороший подход при решении задач с большим количеством действий или длительным процессом обучения. Проще прописать реализацию, нежели тратить время на такое обучение

## Выводы
- В ходе лабораторной работы был разобран алгоритм создания ML-агента и процесс его обучения. Были изучены и описаны параметы из yaml файла, которые помогают в обучении ИИ. А также приведены положительные и отрицательные примеры возмжных внедрений агента в игры. 



## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
