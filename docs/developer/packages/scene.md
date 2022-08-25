---
layout: default
title: Scene
nav_order: 3
parent: Packages
grand_parent: Developer
---

# Scene Package

Scene provides a consistent method of managing scenes and scene transitions. In Unity scenes are used to group related GameObjects to load and unload parts of the game. This could be different UI screens or levels/areas of the game.

---

## SceneReference

Unity's Scene object is only available at runtime and cannot be seen in the Editor. SceneReference provides a way to store a reference to a Scene which is exposed in the Editor. A SceneReference exists for every Scene in the projects Build Setting's Scenes in Build (they are managed by a custom editor script). To make a SceneReference, add the corresponding Scene to Scenes in Build (File > Build Settings), a SceneReference will be automatically created next to the Scene.

## SceneTransition

SceneTransitions provide a consistent, editor friendly way to author transitions between Scenes.

```c#
public class SceneTransition : MonoBehaviour
{
    [SerializeField] SceneReference _loadingScene;
    [SerializeField] SceneReference[] _scenesToLoad;
    [SerializeField] SceneReference[] _scenesToClose;

    public SceneReference LoadingScene => _loadingScene;
    public SceneReference[] ScenesToLoad => _scenesToLoad;
    public SceneReference[] ScenesToClose => _scenesToClose;

    public void PlayTransition()
    {
        EventBus.Publish(new OnSceneTransition(this));   
    }
}
```

## SceneTransitionManager

SceneTransitionManager is a part of the CoreObjects prefab and should always be present in the game. It is Subscribed to the OnSceneTransition event and executes the transition. By calling the SceneTransitions PlayTransition() the transition is picked up by the SceneManager and executed.

The execution of a transition follows this flow: 
1. LoadingScene is loaded
2. ScenesToLoad are loaded into the game
3. ScenesToClose are unloaded from the game
4. LoadingScene is unloaded

Any of these fields can be empty, allowing opening a menu over the existing UI withing closing anything, or transitions without loading scenes.