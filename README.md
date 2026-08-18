# sss

import tkinter as tk

WIDTH = 700
HEIGHT = 500

BALL_SPEED_X = 5
BALL_SPEED_Y = 4

PADDLE_SPEED = 8

player_score = 0
computer_score = 0

ball_dx = BALL_SPEED_X
ball_dy = BALL_SPEED_Y


def move_player(event):
    if event.keysym == "w" or event.keysym == "W":
        canvas.move(player, 0, -PADDLE_SPEED)

  elif event.keysym == "s" or event.keysym == "S":
        canvas.move(player, 0, PADDLE_SPEED)


def move_ball():
    global ball_dx, ball_dy
    global player_score, computer_score

   canvas.move(ball, ball_dx, ball_dy)
    ball_pos = canvas.coords(ball)
    player_pos = canvas.coords(player)
    computer_pos = canvas.coords(computer)

    # برخورد توپ با بالا و پایین صفحه
  if ball_pos[1] <= 0 or ball_pos[3] >= HEIGHT:
        ball_dy = -ball_dy

    # برخورد توپ با بازیکن
  if (
        ball_pos[0] <= player_pos[2]
        and ball_pos[2] >= player_pos[0]
        and ball_pos[1] <= player_pos[3]
        and ball_pos[3] >= player_pos[1]
    ):
        ball_dx = abs(ball_dx)

    # برخورد توپ با کامپیوتر
  if (
        ball_pos[2] >= computer_pos[0]
        and ball_pos[0] <= computer_pos[2]
        and ball_pos[1] <= computer_pos[3]
        and ball_pos[3] >= computer_pos[1]
    ):
        ball_dx = -abs(ball_dx)

    # امتیاز کامپیوتر
  if ball_pos[2] >= WIDTH:
        computer_score += 1
        score_label.config(
            text=f"You: {player_score}    Computer: {computer_score}"
        )
        reset_ball()

    # امتیاز بازیکن
  if ball_pos[0] <= 0:
        player_score += 1
        score_label.config(
            text=f"You: {player_score}    Computer: {computer_score}"
        )
        reset_ball()

   move_computer()

  window.after(20, move_ball)


def move_computer():
    ball_pos = canvas.coords(ball)
    computer_pos = canvas.coords(computer)

   computer_center = (computer_pos[1] + computer_pos[3]) / 2
    ball_center = (ball_pos[1] + ball_pos[3]) / 2

   if computer_center < ball_center:
        canvas.move(computer, 0, 4)

   elif computer_center > ball_center:
        canvas.move(computer, 0, -4)

    # جلوگیری از خارج شدن کامپیوتر از صفحه
   pos = canvas.coords(computer)

   if pos[1] < 0:
        canvas.move(computer, 0, -pos[1])

   if pos[3] > HEIGHT:
        canvas.move(computer, 0, HEIGHT - pos[3])


def reset_ball():
    global ball_dx, ball_dy

   canvas.coords(
        ball,
        WIDTH // 2 - 10,
        HEIGHT // 2 - 10,
        WIDTH // 2 + 10,
        HEIGHT // 2 + 10
    )

   ball_dx = -ball_dx


# ---------------- Window ----------------

window = tk.Tk()
window.title("Ping Pong Game")
window.geometry("700x550")
window.resizable(False, False)

score_label = tk.Label(
    window,
    text="You: 0    Computer: 0",
    font=("Arial", 18, "bold")
)
score_label.pack(pady=10)

canvas = tk.Canvas(
    window,
    width=WIDTH,
    height=HEIGHT,
    bg="black"
)
canvas.pack()

# Player paddle
player = canvas.create_rectangle(
    30, 200,
    45, 300,
    fill="white"
)

# Computer paddle
computer = canvas.create_rectangle(
    655, 200,
    670, 300,
    fill="white"
)

# Ball
ball = canvas.create_oval(
    340, 240,
    360, 260,
    fill="white"
)

# Keyboard controls
window.bind("<KeyPress>", move_player)

window.focus_force()

move_ball()

window.mainloop()
