---
layout: default
title: File Structure
nav_order: 1
parent: General
---

# File Structure

The the project's folders are organized organized by type which loosely maps with role/discipline. 

---

## Art

Contains most of the visual elements of the game. Artists will spend most of their time here.

### Animation

All things animation including Animation Clips, and Animation Controllers. Folders are broken down by specific cases after this.

### Fonts

All the typography of the game. This includes fonts, TMP Font Assets, and TMP Sprite Assets.

### Sprites

The image files for the game. 

---

## Audio

Contains all sounds and related files for the game. Musicians and audio engineers will spend most of their time here.

---

## Configuration

Contains all the configuration Scriptable Objects for the game. Game designers will spend most of their time here.

---

## Prefabs

The prefabs of the game. Character and Environment designers will spend most of their time here.

---

## Scenes

All the scene files of the game. Level and UI designers will spend most of their time here.

---

## Scripts

All the Script files of the game. Developers will spend most of their time here.

### Editor

Where all Unity Editor scripts for the project live. This is anything that will not be used in the game build.

### Runtime

Where all scripts that are used for the actual game are. These will be included in the game build.

#### -Configuration

The Scriptable Object scripts for the objects in the [Configuration](/docs/file-structure/configurations.md) folder.

#### -Controllers

The scripts responsible for logic that updates the games state. See [MVC](/docs/mvc/mvc.md) and [Controllers](/docs/mvc/controller.md) for more info.

#### -Models

The scripts responsible for storing the games state. See [MVC](/docs/mvc/mvc.md) and [Model](/docs/mvc/model.md) for more info.

#### -Views

The scripts responsible for displaying the games state to screen. See [MVC](/docs/mvc/mvc.md) and [Controllers](/docs/mvc/controller.md) for more info.

Next: [Changes](../changes)