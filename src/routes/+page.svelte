<script>
    import "$lib/main.css"
    import { onMount } from "svelte";

    let level_selector
    let plane
    let box
    let others = []

    let cursor_x, cursor_y, box_x, box_y
    let dir_x = 1
    let dir_y = 0

    function handleMouseMovement(event) {
        if (!box) return
        cursor_x = event.clientX - event.target.getBoundingClientRect().left
        cursor_y = event.clientY - event.target.getBoundingClientRect().top
        box_x = box.getPositionX() + 25
        box_y = box.getPositionY() + 25

        let cursor_position_x = cursor_x - box_x 
        let cursor_position_y = cursor_y - box_y
        let maximum = Math.max(Math.abs(cursor_position_x), Math.abs(cursor_position_y))
        let move_strength_x = Math.abs(cursor_position_x) / maximum
        let move_strength_y = Math.abs(cursor_position_y) / maximum
        let move_strength_sum = move_strength_x + move_strength_y
        dir_x = (cursor_position_x < 0 ? -1 : 1) * move_strength_x / move_strength_sum
        dir_y = (cursor_position_y < 0 ? -1 : 1) * move_strength_y / move_strength_sum
    }

    function handleKeyClick(event) {
        console.log(event)
    }

    class Player {
        constructor(isMainCharacter) {
            let random_x = Math.floor(Math.random() * (plane.clientWidth - 50))
            let random_y = Math.floor(Math.random() * (plane.clientHeight - 50))

            this.box = document.createElement("div")
            this.box.style.height = "50px"
            this.box.style.width = "50px"
            this.box.style.position = "absolute"
            this.box.style.left = random_x + "px"
            this.box.style.top = random_y + "px"
            this.box.style.background = "hsl(0, 0%, 50%)"
            this.box.style.borderRadius = "100vh"

            if (isMainCharacter) {
                this.box.style.background = "hsl(220, 75%, 50%)"
                this.box.style.zIndex = 2
            }

            plane.appendChild(this.box)

            this.setSpeed((Math.random() * 4) + 1)
            setInterval(() => this.move(), 1)
        }

        getPositionX() {
            return this.box.getBoundingClientRect().left - plane.getBoundingClientRect().left
        }

        getPositionY() {
            return this.box.getBoundingClientRect().top - plane.getBoundingClientRect().top
        }

        setSpeed(speed) {
            this.speed = speed
        }

        move() {
            let move_x = dir_x * this.speed
            let move_y = dir_y * this.speed

            if (this.getPositionX() + 50 >= plane.clientWidth) {
                if (move_x > 0) move_x = 0
            }
            if (this.getPositionX() <= 0) {
                if (move_x < 0) move_x = 0
            }

            if (this.getPositionY() + 50 >= plane.clientHeight) {
                if (move_y > 0) move_y = 0
            }
            if (this.getPositionY() <= 0) {
                if (move_y < 0) move_y = 0
            }

            this.box.style.left = (this.getPositionX() + move_x) + "px"
            this.box.style.top = (this.getPositionY() + move_y) + "px"
        }
    }

    let levels = [
        () => {
            box = new Player(true)
            others = [new Player(), new Player(), new Player()]
        }
    ]

    function startLevel(num) {
        level_selector.style.display = "none"
        plane.style.display = "block"
        levels[num]()
    }
</script>

<!--  -->

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="plane" bind:this={plane} on:mousemove={handleMouseMovement} on:keydown={handleKeyClick}></div>

<div class="level-selector" bind:this={level_selector}>
    <button on:click={() => startLevel(0)}>Level 1</button>
</div>

<!--  -->

<style>
    .plane{
        position: relative;
        height: 90vh;
        width: 90vw;
        margin: 5vh 5vw;
        background: rgb(25 25 25);
        display: none;
    }

    .level-selector{
        padding: 20px;
        margin: 20px;
        width: fit-content;
        background: rgb(100 20 20);
    }
    
    button{
        all: unset;
        display: block;
        padding: 10px 20px;
        background: rgb(250 225 225);
    }

    button:hover{
        background: rgb(225 200 200);
    }

    button:active{
        background: rgb(250 250 250);
    }
</style>