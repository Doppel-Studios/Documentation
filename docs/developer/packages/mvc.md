---
layout: default
title: MVC
nav_order: 2
parent: Packages
grand_parent: Developer
---

# Model View Controller (MVC) Package

MVC is a software architectural pattern commonly used for developing user interfaces that divide the related program logic into three interconnected elements. This is done to separate internal representations of information from the ways information is presented to and accepted from the user. ([Wikipedia](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller))

---

## Components

More details about each type of component can be found in their respective page.

### Model

Models are where all the information about the games state is stored. They provide one "source of truth" about the game and provide an easy way to access information about the current state in other scripts like Views and Controllers.

The Template Project only has one model right now, GameModel.cs. To access models use the ModelFactory Class.

```c#
void Start()
{
    _gameModel = ModelFactory.GetModel<GameModel>();
}
```

Inside is where we have the objects responsible for managing the games state. This often takes the forms of defining Event classes and holding instances of EventData variables (see [Event Driven Architecture](../event)). 

A good example of a Model is GameModel.cs.

```c#
public class GameModel : Model
{
    public EventData<GameConfiguration> gameConfiguration = new EventData<GameConfiguration>();
    public EventData<int> score = new EventData<int>();
    public EventData<int> health = new EventData<int>();

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

    public class OnLoadLevel : Event { }

    public class OnVictory : Event { }

    public class OnFailure : Event { }

    public class OnPlayAgain : Event { }

    public class OnQuitGameplay : Event { }

    public class OnRestart : Event { }

    public class OnExit : Event { }
}
```

### View

Views are how the current state of the game is represented to the player. The environment, characters, and UI use views to read Models and present the information in an understandable way.

Views are MonoBehaviours that subscribe to events and EventData to be informed of state changes and render the results to the player. This includes UI and game components. Avoid having vies modify the state of the game.

A good example of a View is PlayerInfoView.cs.

```c#
public class PlayerInfoView : MonoBehaviour
{
    [SerializeField] TMP_Text _scoreText;
    [SerializeField] string _scoreFormat = "Points: {0}";
    [SerializeField] TMP_Text _healthText;
    [SerializeField] string _healthFormat = "Health: {0}";

    GameModel _gameModel;

    void Start()
    {
        _gameModel = ModelFactory.GetModel<GameModel>();

        _gameModel.score.Subscribe(OnPointsChanged);
        _gameModel.health.Subscribe(OnHealthChanged);
    }

    private void OnDestroy()
    {
        _gameModel.score.Unsubscribe(OnPointsChanged);
        _gameModel?.health.Unsubscribe(OnHealthChanged);
    }

    void OnPointsChanged(OnChanged<int> e)
    {
        _scoreText.text = string.Format(_scoreFormat, e.newValue);
    }

    void OnHealthChanged(OnChanged<int> e)
    {
        _healthText.text = string.Format(_healthFormat, e.newValue); 
    }
}
```

### Controller

Controllers are responsible for handling updates to the games state from user input or scripting logic. They change values in Models to represent these updates.

Controllers are MonoBehaviours that both subscribe to and publish events. They take the games current state along with any user input, and modify the state accordingly. 

A good example of a Controller is PlayerController.cs.

```c#
public class PlayerController : MonoBehaviour
{
    GameModel _gameModel;

    void Start()
    {
        _gameModel = ModelFactory.GetModel<GameModel>();

        EventBus.Subscribe<GameModel.OnAddPoints>(OnAddPoints);
        EventBus.Subscribe<GameModel.OnTakeDamage>(OnTakeDamage);
    }

    void OnDestroy()
    {
        EventBus.Unsubscribe<GameModel.OnAddPoints>(OnAddPoints);
        EventBus.Unsubscribe<GameModel.OnTakeDamage>(OnTakeDamage);
    }

    void OnAddPoints(GameModel.OnAddPoints e)
    {
        _gameModel.score.Value += e.pointsAdded;
    }

    void OnTakeDamage(GameModel.OnTakeDamage e)
    {
        _gameModel.health.Value -= e.damageTaken;
    }
}
```