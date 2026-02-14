# Rock-Paper-Scissors
import random

moves = ["R", "P", "S"]
win_map = {"R": "P", "P": "S", "S": "R"}
lose_map = {"R": "S", "P": "R", "S": "P"}


def player(prev_play, opponent_history=[], my_history=[]):
    # Reset histories for new match
    if prev_play == "":
        opponent_history.clear()
        my_history.clear()
        return random.choice(moves)

    opponent_history.append(prev_play)

    # Simple n-gram prediction
    n = 3  # length of sequence to look back
    if len(opponent_history) > n:
        last_n = "".join(opponent_history[-n:])
        # Search for last_n in history and see what followed most often
        look_back = min(20, len(opponent_history) - n)
        seq_start = len(opponent_history) - n - look_back
        if seq_start < 0:
            seq_start = 0
        search_history = "".join(opponent_history[seq_start:-1])

        # Find all occurrences of last_n in search_history and collect following move
        following_moves = []
        for i in range(len(search_history) - n):
            if search_history[i:i + n] == last_n:
                following_moves.append(search_history[i + n])

        if following_moves:
            # Predict most frequent following move
            prediction = max(set(following_moves), key=following_moves.count)
            guess = win_map[prediction]
        else:
            # Fallback to countering most frequent recent move
            recent = opponent_history[-10:]
            most_frequent = max(set(recent), key=recent.count)
            guess = win_map[most_frequent]
    else:
        # Not enough history yet, play randomly
        guess = random.choice(moves)

    my_history.append(guess)
    return guess
