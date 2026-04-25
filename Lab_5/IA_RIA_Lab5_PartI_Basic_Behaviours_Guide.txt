IA / RIA LAB 5 - PART I GUIDE: BASIC BEHAVIOURS

Goal:
Create a herbivorous animal model. Turtles move, lose energy, feed, reproduce, and die. Patches contain food and regenerate after a delay.

Interface sliders to add:
- initial-energy: 0..100, step 1, initial 20
- food-energy: 0..50, step 1, initial 10
- move-cost: 0..5, step 0.1, initial 1
- food-regeneration-time: 1..100, step 1, initial 30
- reproduction-energy: 1..100, step 1, initial 40
- reproduction-chance: 0..100, step 1, initial 5
- hunger-threshold: 0..100, step 1, initial 25, for extra task

Monitors:
- count turtles
- ifelse-value any? turtles [mean [energy] of turtles] [0]
Optional:
- count patches with [regeneration-timer = 0]
- count patches with [regeneration-timer > 0]

Part I code:

turtles-own [ energy ]
patches-own [ regeneration-timer ]

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
    set pcolor green
  ]
end

to move
  ask turtles [
    right random 50
    left random 50
    forward 1
    set energy energy - move-cost
  ]
end

to feed
  ask turtles [
    if regeneration-timer = 0 [
      set energy energy + food-energy
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
      set pcolor green
    ]
  ]
end

Extra hunger-based feed version:
Replace feed with this if doing the extra task:

to feed
  ask turtles [
    if energy < hunger-threshold [
      if regeneration-timer = 0 [
        set energy energy + food-energy
        ask patch-here [
          set regeneration-timer food-regeneration-time
          set pcolor black
        ]
      ]
    ]
  ]
end

Experiment examples:
Extinction: number-turtles 80, initial-energy 10, food-energy 5, move-cost 2, food-regeneration-time 80, reproduction-energy 60, reproduction-chance 5.
Accelerated reproduction: number-turtles 30, initial-energy 30, food-energy 20, move-cost 0.5, food-regeneration-time 10, reproduction-energy 35, reproduction-chance 30.
Stabilization: number-turtles 40, initial-energy 25, food-energy 10, move-cost 1, food-regeneration-time 30, reproduction-energy 45, reproduction-chance 8, hunger-threshold 25.
