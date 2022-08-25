---
layout: default
title: Event
nav_order: 1
parent: Packages
grand_parent: Developer
---

# Event Package

In computer programming, event-driven programming is a programming paradigm in which the flow of the program is determined by events such as user actions (mouse clicks, key presses), sensor outputs, or message passing from other programs or threads. Event-driven programming is the dominant paradigm used in graphical user interfaces and other applications (e.g., JavaScript web applications) that are centered on performing certain actions in response to user input.

In an event-driven application, there is generally a main loop that listens for events and then triggers a callback function when one of those events is detected.

---

## Event

Events are objects that represent a change in state. This could be a restarting the game, a unit taking damage, or a timer value changing.
By extending the Event class different events can provide their specific details. These can be defined anywhere but generally exits in the games Model files. 

The following examples of Events can be found in GameModel.cs. OnAddPoints represents the player gaining a specific amount of points, while OnTakeDamage represents the player taking a specific amount of damage.

```c#
public class OnAddPoints : Event
{
    public int pointsAdded;

    public OnAddPoints(int pointsAdded)
    {
        this.pointsAdded = pointsAdded;
    }
}

public class OnTakeDamage : Event
{
    public int damageTaken;

    public OnTakeDamage(int damageTaken)
    {
        this.damageTaken = damageTaken;
    }
}
```

## Callback

A callback is a method that takes in an instance of an event and contains the logic that should be run in response to it.

Callbacks for the above example Events can be found in PlayerController.cs. Although they could be named anything the convention is to match the name of the event. The OnAddPoints callback adds the events pointsAdded to the players score, and OnTakeDamage subtracts the events damageTaken from the players health.

```c#
void OnAddPoints(GameModel.OnAddPoints e)
{
    _gameModel.score.Value += e.pointsAdded;
}

void OnTakeDamage(GameModel.OnTakeDamage e)
{
    _gameModel.health.Value -= e.damageTaken;
}
```

## Event Bus

The Event Bus is a globally accessible static class which provides the ability to Subscribe callbacks to events or publish an event to execute all its subscriber callbacks. 

The PlayerController tells the EventBus to subscribes to the example Events with their respective callbacks. Wherever we have subscriptions we need to be sure to Unsubscribe eventually to prevent dead connections.

```c#
void Start()
{
    ...
    EventBus.Subscribe<GameModel.OnAddPoints>(OnAddPoints);
    EventBus.Subscribe<GameModel.OnTakeDamage>(OnTakeDamage);
}

void OnDestroy()
{
    EventBus.Unsubscribe<GameModel.OnAddPoints>(OnAddPoints);
    EventBus.Unsubscribe<GameModel.OnTakeDamage>(OnTakeDamage);
}
```

To Execute all an events subscriber callbacks the EventBus needs to publish an instance of that event. GameplayController.cs tells the EventBus to publish our example Events whenever their respective buttons are clicked.

```c#
void Start()
{
    ...
    _addPointsButton.onClick.AddListener(() => EventBus.Publish(new GameModel.OnAddPoints(_gameModel.gameConfiguration.Value.PointsReward)));
    _takeDamageButton.onClick.AddListener(() => EventBus.Publish(new GameModel.OnTakeDamage(_gameModel.gameConfiguration.Value.DamageToTake)));
    ...
}
```

## EventData

The EventData class holds a value of any type of object and provides its own Subscribe method which allows callback subscriptions for when its value is changed.

```c#
void Start()
{
    ...
    _gameModel.score.Subscribe(OnPointsChanged);
    _gameModel.health.Subscribe(OnHealthChanged);
}

private void OnDestroy()
{
    _gameModel.score.Unsubscribe(OnPointsChanged);
    _gameModel?.health.Unsubscribe(OnHealthChanged);
}
```

Examples of creating EventData can be found in GameModel.cs.

```c#
public EventData<GameConfiguration> gameConfiguration = new EventData<GameConfiguration>();
public EventData<int> score = new EventData<int>();
public EventData<int> health = new EventData<int>();
```

The callback method for EventData Subscriptions takes an OnChanged class, which extends Event, with the type of the EventData's value. Convention is to call the callback On(EventData name)Changed and the OnChanged<T> parameter e. This OnChanged class contains both the oldValue and newValue of the change. The following examples from GamePlayController.cs send the player to the victory or failure screen depending on the new value of the event.

```c#
void OnPointsChanged(OnChanged<int> e)
{
    if(e.newValue >= _gameModel.gameConfiguration.Value.PointsToWin)
    {
        _victoryTransition.PlayTransition();
    }
}

void OnHealthChanged(OnChanged<int> e)
{
    if (e.newValue <= 0)
    {
        _failureTransition.PlayTransition();
    }
}
```