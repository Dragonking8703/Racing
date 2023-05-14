# Racing
extends Node2D

var car_speed = 300

var car_rotation_speed = 2

var obstacle_speed = 200

var score = 0

var game_over = false

onready var score_label = $ScoreLabel

onready var game_over_label = $GameOverLabel

onready var restart_button = $RestartButton

func _ready():

    set_process_input(true)

    set_fixed_process(true)

    game_over_label.hide()

    restart_button.hide()

func _process(delta):

    if not game_over:

        if Input.is_action_pressed("left"):

            $Car.rotate(car_rotation_speed * delta)

        elif Input.is_action_pressed("right"):

            $Car.rotate(-car_rotation_speed * delta)

func _fixed_process(delta):

    if not game_over:

        var direction = $Car.rotation + pi / 2

        var velocity = Vector2(cos(direction), sin(direction)) * car_speed * delta

        $Car.position += velocity

        var screen_size = get_viewport_rect().size

        $Car.position.x = clamp($Car.position.x, 0, screen_size.x)

        $Car.position.y = clamp($Car.position.y, 0, screen_size.y)

        for obstacle in $Obstacles.get_children():

            obstacle.position.y += obstacle_speed * delta

            if obstacle.position.y > screen_size.y:

                obstacle.position.y = -obstacle.texture.get_height()

                obstacle.position.x = rand_range(0, screen_size.x - obstacle.texture.get_width())

            if $Car.get_rect().intersects(obstacle.get_rect()):

                game_over = true

                game_over_label.show()

                restart_button.show()

    score += 1

    score_label.text = "Score: " + str(score)

func _input(event):

    if event.is_action_pressed("restart"):

        get_tree().reload_current_scene()


