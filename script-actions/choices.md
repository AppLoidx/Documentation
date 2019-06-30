# Choices

But what is a Visual Novel without choices right?

Choices are built as JSONs, with the key 'Choice', and every choice available as a sub-item.

Every choice requires 2 properties: the text to display and what to do when it's clicked.

```javascript
{'Choice': {
    'Developer': {
        'Text': 'I’m a developer.',
        'Do': 'jump Developer'
    },
    'Writer': {
        'Text': 'I’m a writer.',
        'Do': 'jump Writer'
    },
    'Artist': {
        'Text': 'I’m an artist.',
        'Do': 'jump Artist'
    },
    'Player': {
        'Text': 'I’m a Player.',
        'Do': 'jump Player'
    }
}}
```

## Conditional Choices

There are some cases where you would only want to show a choice if certain conditions are met. Conditional choices are possible by adding a simple 'Condition' function.

For this example, let's assume this is your `storage` variable:

```javascript
'use strict';
// Persistent Storage Variable

Monogatari.storage ({
    played: false
});
```

As you can see, we added a 'played' variable inside the storage and set its default to `false`. Now, let's see what would happen with the following Choices:

```javascript
{'Choice': {
    'Developer': {
        'Text': 'I’m a developer.',
        'Do': 'Developer'
    },
    'Player': {
        'Text': 'I’m a Player.',
        'Do': 'Player',
        'Condition': function () {
            return this.storage ('played'); // The 'Player' option will only be shown if this returns true.
        }
    }
}}
```

`'Player'` will only be shown if the `'played'` variable we added is `true`, since we defined it as `false`, then it won't be shown however, if somewhere along the script we were to change that variable to `true` then this choice will be shown.

## Showing Text when Showing the Choices

You might want to show a dialog along with the choices, this is possible adding a text property inside the object:

```javascript
{'Choice': {
    'Dialog': 'e Before I continue, let me ask you, what are you?',
    'Developer': {
        'Text': 'I’m a developer.',
        'Do': 'Developer'
    },
    'Player':{
        'Text': 'I’m a Player.',
        'Do': 'Player',
        'Condition': function () {
            return this.storage ('played'); // The 'Player' option will only be shown if this returns true.
        }
    }
}}
```

That way the `'Dialog'` property is the one that will be shown with the choices, as you can see, just like with normal dialogs, you can specify what character is the one talking and take full advantage of other things like side images on it.

## Unclickable Choices

Earlier we made a conditional choice that would only be shown if a condition was satisfied, but what if you want to show the player that there is a choice they _could_ be making if certain conditions were satisfied.

For this example, let's assume this is your `storage` variable:

```javascript
'use strict';
// Persistent Storage Variable
Monogatari.storage ({
    haveKey: false
});
```

As you can see, we added a 'haveKey' variable inside the storage and set its default value to `false`. With that established, consider the following code.

```javascript
{'Choice':{
    'Dialog': 'Do you unlock the locked door?',
    'FirstOption':{
        'Text': 'Yes',
        'Do': 'jump label1',
        'Clickable': function(){
            return Monogatari.storage().haveKey
        }
},
    'SecondOption':{
        'Text': 'No',
        'Do': 'jump label2'
    }
}},
```

The `'Clickable'` property, as its name implies, decides whether or not a button can be clicked.

`Yes` will be shown regardless of whether or not the variable we added is `true`, but it will only be _clickable_ if it is. On that note, if the variable `haveKey` is false, then the `Yes` button will not be clickable. However, this might be confusing to your players, as they will see a choice, try to click it, and clicking it will do nothing. We can help out with this confusion with a little bit of visual design.

## Styling your Buttons

The `'Class'` property allows you to give a CSS class to your buttons for special styles. Suppose you had the following CSS in your `main.css` file:

```css
.boldedText {
    font-weight: bold;
}
.italicText {
    font-style: italic;
}
```

The `boldedText` and `italicText` classes can then be applied to your buttons by setting their `'Class'` properties in your script, like so:

```javascript
{"Choice":{
    "FirstOption":{
        "Text": "Yes",
        "Do": "jump label1",
        "Class": "boldedText" //A css class to apply to the button.
},
    "SecondOption":{
        "Text": "No",
        "Do": "jump label2",
        "Class": "italicText"
    }
}},
```

This way, the `'Yes'` button will have its text **bolded** and the `'No'` button will have its text _italicized_. You can also use CSS styling to do things like give buttons background images and make the text on them invisible, so you can have buttons with pictures!

Earlier, we mentioned you could style unclickable buttons as well. This is easy to do as the buttons in Monogatari's choices are HTML buttons, and when a button is made unclickable, it is given the `'disabled'` attribute. This means that we can style disabled buttons with CSS attribute styling. Consider the following example:

```css
[disabled] {
    opacity: .5;
}
```

With this CSS styling, buttons containing the `'disabled'` attribute will render semi-transparent, which will visually indicate to the player that they are different, so hopefully they won't be confused when they are unable to click it.

You can also use classes and attributes at the same time:

```css
[disabled].classname {
    text-decoration: line-through;
}
```

In this example, the text would have a line through it if and only if it is both disabled, and the button has the `'.classname'` class. If you wanted to make a specific class for a specific button, you could use the `::after` pseudo selector and the `'content'` property to append an explanation as to why you can't click the button too, or you could make two separate buttons where one displays if a condition is met, and the other displays if the condition is unmet.

## onChosen Functions

The `'onChosen'` property allows us to run a function when the player clicks your button. For this example, we'll assume that our storage contains the following:

```javascript
'use strict';
// Persistent Storage Variable

Monogatari.storage ({
    enemyHealth: 100,
    playerAttack: 20
});
```

In our `main.js` file, we could have a simple example function for attacking the enemy.

```javascript
function attackEnemy(){
    enemyHealth = enemyHealth - playerAttack;
}
```

And then in our script, we could have a choice for the player.

```javascript
{"Choice":{
    "Dialog": "The enemy wants to fight! What do you do?",
    "FirstOption":{
        "Text": "Attack!",
        "onChosen": attackEnemy(),
        "Do": "You attack the enemy for {{playerAttack}} damage!"
},
    "SecondOption":{
        "Text": "Defend",
        "Do": "You defend instead of attacking."
    }
}},
"The enemy now has {{enemyHealth}} health points left."
```

You could also have the function written out in the script itself for one-time functions that won't be called again.

```javascript
{"Choice":{
    "Dialog": "The enemy wants to fight! What do you do?",
    "FirstOption":{
        "Text": "Attack!",
        "onChosen": function attackEnemy(){
            enemyHealth = enemyHealth - playerAttack;
        },
        "Do": "You attack the enemy for {{playerAttack}} damage!"
},
    "SecondOption":{
        "Text": "Defend",
        "Do": "You defend instead of attacking."
    }
}},
"The enemy now has {{enemyHealth}} health points left."
```

This achieves the same result. You could also use these functions to invisibly keep track of which options the player clicked, or any other ideas you might have.

