---
layout: post
title: "[Python] Twilio Quest : The Pythonic Temple"
date: 2022-03-24
excerpt: "Python Algorithm"
tags: [Python, Algorithm, Grammer]
comments: false
---

# Twilio Quest
  - https://www.twilio.com/quest
  - TwilioQuest is an educational video game designed to teach a new generation of developers how to change the world with code.
  - From learning how to use your terminal, to coding in Python, JavaScript, and contributing to Open Source projects, TwilioQuest will help you develop practical engineering skills.

# import sys

## Module
  - In python, we can use module to import code which is made by others.
  - From modules, we can make our program easier.

### Sys
  - Sys provides how to control python interpreter.

{% highlight py %}
import sys

sentence = sys.argv[1]

print(sentence.upper() + "!!!")
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/python_twilioquest/1.jpg"><img src="/assets/img/posts/python_twilioquest/1.jpg"></a>
	<figcaption>Python Twilio Quest strings.py</figcaption>
</figure>

# Formatting
  - In python, we use `f` for string formatting.

{% highlight py %}
import sys

words = sys.argv
words.pop(0)

for word in words:
    print(f"{word}")
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/python_twilioquest/2.jpg"><img src="/assets/img/posts/python_twilioquest/2.jpg"></a>
	<figcaption>Python Twilio Quest collect_input.py</figcaption>
</figure>

* pop()
  - Left variable gets last value of list.
  - And List will lose last value.
  - When you input an index in blank, left variable gets indexed value of list and list will lose the value.

# Class

## __init__(self)
  - we use this for initializing and make instances.

## def 
  - We can create our functions with `def`
  - You can also make function outside of a class and it's not included in a class.
  - But when we use `def` inside of a class, we should use `self` too.

{% highlight py %}
class Citizen:
    '''Return full name'''
    
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name

    def full_name(self):
        return f"{self.first_name} {self.last_name}"

    greeting = "For the glory of Python!"
{% endhighlight %}

# Boggle
  - Boggle is a word game.
  - Player find words in the board and player can link letters by up, down, left, rifht, even diagonal.

<figure>
  <a href="/assets/img/posts/python_twilioquest/3.jpg"><img src="/assets/img/posts/python_twilioquest/3.jpg"></a>
	<figcaption>Python Twilio Quest Boggle</figcaption>
</figure>

## Code

{% highlight py %}
import nltk
from nltk.corpus import words

nltk.download('words')
dictionary = words.words()

board = [['r', 'n', 'o'],
         ['a', 'j', 's'],
         ['p', 'y', 'f']]

def solve(board, dictionary):
    max_lengths = {}
    for word in dictionary:
        if word[0] not in max_lengths:
            max_lengths[word[0]] = min(len(word), 9)
        else:
            max_lengths[word[0]] = max(min(len(word), 9), max_lengths[word[0]])

    found = {}

    for i, row in enumerate(board):
        for j, letter in enumerate(row):
            max_length = 0
            if letter in max_lengths:
                max_length = max_lengths[letter]
            check_the_combined_word(board, i, j, max_length, letter, [[i, j]], found)

    return found

def check_the_combined_word(board, x, y, max_length, combined_word, green_ones, found):
    at = lambda cell: board[cell[0]][cell[1]]

    if len(combined_word) > max_length:
        return

    if len(combined_word) <= max_length:
        if (combined_word in dictionary) and (len(combined_word) == 5):
            found[combined_word] = 1

    nbrs = neighbors([x, y], board)
    green_ones.append([x, y])

    for n in nbrs:
        if not_visited(n, green_ones.copy()):
            check_the_combined_word(board, n[0], n[1], max_length, combined_word + at(n), green_ones, found)
    green_ones.pop()

def neighbors(point, board):
    num_rows = len(board)
    num_cols = len(board[0])

    ret = []
    for next_cell in [[+1, 0],
                      [-1, 0],
                      [0, -1],
                      [0, +1],
                      [+1, +1],
                      [-1, +1],
                      [-1, -1],
                      [+1, -1]]:
        if (0 <= point[0] + next_cell[0] < num_rows) and (0 <= point[1] + next_cell[1] < num_cols):
            ret.append([point[0] + next_cell[0], point[1] + next_cell[1]])
    return ret

def not_visited(x, visited):
    L = len(list(filter(lambda  o: o[0] == x[0] and o[1] == x[1], visited)))
    return L == 0

found = solve(board, dictionary)
L = list(found.keys())
if len(L) == 0:
    print(-1)
else:
    print(" ".join(sorted(L)))
{% endhighlight %}

### Module
  - I used nltk because it has `words()` function which get words in used.

### Functions

* solve(board, dictionary)
  - Limit max length of word. In this quest, our answer is 5 letters so our max length is equal or less than 5.

* check_the_combined_word(board, x, y, max_length, combined_word, green_ones, found)
  - go to neighbor position in grid and add that letter in our combined word.

* neighbors(point, board)
  - find neighbor position we can visit.

* not_visited(x, visited)
  - ban letter to be used more than once.

<figure>
  <a href="/assets/img/posts/python_twilioquest/4.jpg"><img src="/assets/img/posts/python_twilioquest/4.jpg"></a>
	<figcaption>Python Twilio Quest Boggle</figcaption>
</figure>

[Download](https://github.com/leehuhlee/Python){: .btn}