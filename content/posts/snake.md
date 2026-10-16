+++
title = "Snake"
date = 2025-04-07
description = "A snake clone created with Bevy."
+++

Continuing Rust practice by creating a Snake clone with Bevy.

Links:
- [Bevy](https://bevyengine.org/)
- [Source Code](https://github.com/dlittleton/snake-bevy)

## Game

{{ bevy_canvas(path='assets/snake/snake-bevy.js', width="1280", height="720") }}

Use WASD or arrow keys to control.

## Thoughts on Bevy

I'm not terribly familiar with the Entity Component System approach to games and
I feel like I might not have used it as effectively as I could have. In the end
I ended up with a large `Game` resource that kept track of collision data, snake
position, and movement directions. This feels somewhat like it's working against
the system rather than tracking those properties as components within the snake
entity. This approach also meant that I made minimal use of the `Query` APIs
since nearly everything I needed to work with was accessible right in the `Game`
resource.

I liked the ability to define an `enum` to represent different game
states. Coupled with the ability to spawn entities as `StateScoped`, this made
it really easy to transition between the title screen and game screen without
having to worry about any manual cleanup.

I also appreciated the support for grouping things across the API. `Components`
can be grouped into `Bundles`. `Systems` (and many other things) can be grouped
into `Plugins`. These features seem like they provide a clear path for wrangling
the complexity as a project grows.

Overall I would definitely consider using it again. I'd like to give the ECS
approach another try with a different game.

## Output File Sizes

If I had to pick something to complain about, it would have to be the size of
the output files when compiling to `wasm`. The
[documentation](https://bevy-cheatbook.github.io/platforms/wasm/size-opt.html)
has some helpful pointers for this though that seemed to work fairly well.

Here are the sizes I ended up with trying the different recommendations. The
`lto = true` setting did more than double the compile time, but the space
savings still seem worth it for producing the release build.

| cargo flags | wasm-opt flags | Output Size (bytes) |
|-------------|------------------|-------------|
| None | None | 56422822 |
| None | `-Oz` | 30939472 |
| `opt-level = 'z'`| None | 49929119 |
| `opt-level = 'z'`| `-Oz` | 23074894 |
| `opt-level = 'z'`, `lto = true` | None | 25064435 |
| `opt-level = 'z'`, `lto = true` | `-Oz` | 15821234 |
