---
title: "ssshmup"
date: 2020-05-18
# weight: 1
# aliases: ["/first"]
tags: ["rust", "gamedev"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://blog.mikail-khan.com/posts/tetrs"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

A few weeks ago I wrote a super small shoot 'em up. I started because some friends of mine were making a similar shoot 'em up in Unity, and given that they actually have good artists, they had some nice assets. I didn't want to work with them on it but I decided that I'd take the chance to make my own, much smaller take on it. I called it ssshmup.

As usual, I wrote it in Rust with `ggez` for rendering, input, and the event loop. I also used `specs`, an entity-component-system (ECS) library.

I generally start ECS programs by brainstorming the types of entities needed and the best way to separate them into components. For most games, the obvious starting components are Position and Velocity, and then the obvious starting System to go with them is for integration.


```rust
#[derive(Clone, Copy, Debug, PartialEq, Component)]
#[storage(VecStorage)]
pub struct Position(pub Point);

#[derive(Clone, Copy, Debug, PartialEq, Component)]
#[storage(VecStorage)]
pub struct Velocity(pub Vector);

pub struct IntegrateSys;
impl<'a> System<'a> for IntegrateSys {
    type SystemData = (WriteStorage<'a, Position>, ReadStorage<'a, Velocity>);

    fn run(&mut self, (mut positions, vels): Self::SystemData) {
        (&mut positions, &vels).par_join().for_each(|(pos, vel)| {
            pos.0 += vel.0;
        });
    }
}
```

For ssshmup, the next entity type I needed to make was for the player. Here's the data that a player needs to have:
- position
- velocity
- HP
- sprite
- bullet type
- reload timing info
- deflector timing info
- hitbox info

Out of that, Position and Velocity are already separated into components, and HP and Sprite aren't unique to player. On the other hand, bullet type, reload info, and deflector timing info are unique to the player. With that, I needed a few more components:

- HP
- Sprite
- Player
- Hitbox

I also created a helper type, `PlayerTuple`:
```rust
type PlayerTuple = (Position, Velocity, HP, Sprite, Player, Hitbox);
```

From an ECS perspective, enemies aren't really that different from the player. Here's an EnemyTuple
```rust
type EnemyTuple = (Position, Velocity, HP, Sprite, Enemy, Hitbox);
```

In the actual code, things are slightly different because of other complexities but this is pretty close.

The next major type is bullet. Bullets are even simpler than enemies and players, but they still do have some unique data.
```rust
type BulletTuple = (Position, Velocity, Sprite, Bullet, Hitbox);
```

With just a few components, we can model almost the entirety of the game. Here's the final list of components I used:
- Position
- Velocity
- Player
- ColorRect (for drawing the starts in the background)
- HP
- Enemy
- Bullet
- Sprite
- AnimatedSprite - (for explosions)
- Hitbox

To create interactions between all these components, I wrote some systems. They're all mostly self-explanatory.

- EnemyMoveSys - For making enemies move left and right or up and down
- BulletTrackingSys - For making tracking bullets track the player
- BounceBulletSys - For making bouncing bullets bounce
- IntegrateSys - For integrating all velocities to positions 
- StarMoveSys - The particle system for stars
- ReloadTimerSys - For player reloads
- DeflectorSys - For player deflector
- EnemyShootSys - For enemy shooting
- AnimationSys - For `AnimatedSprite`s
- BulletCollSys - For bullet collisions with anything
- PlayerCollSys - For player collisions with enemies
- HPKillSys - For deleting entities that have an `HP` of 0
- IFrameSys - For immunity frames after player collisions, this was mostly needed to change opacity when something is immune

You can find ssshmup at [github.com/mkhan45/ssshmup](https://github.com/mkhan45/ssshmup)
