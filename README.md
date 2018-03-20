# game-of-life

> [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)

[Demo](https://tsauvajon.github.io/game-of-life/)

The rules of the Game of Life are simple :
- A cell with too few (less than 2 - *underpopulation*) or too many
(more than 3 - *overpopulation*) neighbours will die
(change from colored to white)
- A cell will spawn (change from with to colored) if it
has 3 neighbours (*reproduction*)

You can check some example patterns on
[Wikipedia](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life#Examples_of_patterns).

More info on the Game of Life at
[http://www.conwaylife.com](http://www.conwaylife.com) or
[Stanford University](http://web.stanford.edu/~cdebs/GameOfLife/).

Better (for now !) simulators are avaible on the Internet :
- [Bitstorm](https://bitstorm.org/gameoflife/)
- [Stanford University](http://web.stanford.edu/~cdebs/GameOfLife/)
- ...

## Savefiles

Use `.lif` 1.06 save files to load patterns on the canvas.
You can find `.lif` files in the `savefiles` folder, and hundred
of other patterns at
[Conway Life : patterns](http://www.conwaylife.com/wiki/Category:Patterns)

Just paste the .lif content on the text area and click `Load`.
To dump your current configuration to a `.lif` format, click `Dump`
and copy the text area's content.

## Build Setup

``` bash
# install dependencies
yarn

# serve with hot reload at localhost:8080
yarn dev

# build for production with minification
yarn build
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
