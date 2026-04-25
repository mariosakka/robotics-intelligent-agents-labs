IA / RIA LAB 5 - PART II GUIDE: SIMPLE NAVIGATION

Goal:
Extend Part I so food is distributed unevenly, nutrient-rich food gives more energy, and turtles search for the best food inside a limited field of view.

Interface controls to add:
- rich-food-percent: 0..100, step 1, initial 20
- rich-food-energy: 0..100, step 1, initial 25
- vision-radius: 1..20, step 1, initial 6
- vision-angle: 10..360, step 5, initial 120
- guided-movement? switch, default on

Part II code:

turtles-own [ energy ]
patches-own [ regeneration-timer food-value ]

to setup
  clear-all
  setup-patches
  setup-turtles
  reset-ticks
end

to go
  if not any? turtles [ stop ]
  move
  feed
  reproduce
  death
  grow-food
  tick
end

to setup-turtles
  create-turtles number-turtles [
    setxy random-xcor random-ycor
    set heading random 360
    set color brown
    set shape "sheep"
    set size 1.2
    set energy initial-energy
  ]
end

to setup-patches
  ask patches [
    set regeneration-timer 0
    ifelse random 100 < rich-food-percent [
      set food-value rich-food-energy
      set pcolor lime
    ] [
      set food-value food-energy
      set pcolor green
    ]
  ]
end

to move
  ask turtles [
    ifelse guided-movement? [ guided-move ] [ random-move ]
  ]
end

to random-move
  right random 50
  left random 50
  forward 1
  set energy energy - move-cost
end

to guided-move
  let target max-one-of patches in-cone vision-radius vision-angle with [regeneration-timer = 0] [food-value]
  ifelse target != nobody [
    face target
  ] [
    right random 50
    left random 50
  ]
  forward 1
  set energy energy - move-cost
end

to feed
  ask turtles [
    if regeneration-timer = 0 [
      set energy energy + food-value
      ask patch-here [
        set regeneration-timer food-regeneration-time
        set pcolor black
      ]
    ]
  ]
end

to reproduce
  ask turtles [
    if energy >= reproduction-energy [
      if random 100 < reproduction-chance [
        set energy energy / 2
        hatch 1 [
          set energy [energy] of myself
          right random 360
          forward 1
        ]
      ]
    ]
  ]
end

to death
  ask turtles [
    if energy <= 0 [ die ]
  ]
end

to grow-food
  ask patches [
    ifelse regeneration-timer > 0 [
      set regeneration-timer regeneration-timer - 1
      set pcolor black
    ] [
      ifelse food-value = rich-food-energy [
        set pcolor lime
      ] [
        set pcolor green
      ]
    ]
  ]
end

Navigation explanation:
max-one-of patches in-cone vision-radius vision-angle with [regeneration-timer = 0] [food-value]
selects the best visible food patch.
face target turns the turtle toward it.

Bonus species idea:
Use two breeds:
breed [randoms random-animal]
breed [guideds guided-animal]
Randoms use random-move. Guideds use guided-move.

Useful monitors:
- count randoms
- count guideds
- ifelse-value any? randoms [mean [energy] of randoms] [0]
- ifelse-value any? guideds [mean [energy] of guideds] [0]

Experiments:
Low rich food: rich-food-percent 5, vision-radius 6, vision-angle 120.
High rich food: rich-food-percent 40, vision-radius 8, vision-angle 180.
Narrow vision: vision-angle 30.
Wide vision: vision-angle 360.
