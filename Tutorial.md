# Dialogue System Tutorial
> In this tutorial we will be going over how to make a simple dialogue system in Unity.

### Setting up the scene

> Create a new scene in Unity. Create a panel with two text elements and a button childed onto it. One text element will hold an NPC's
> name, so name it "NPCName". Change its text to say "NPC Name" Name the other text element "dialogue". Put some sample dialogue 
> there if you like. The button will be used to continue to the next bit of dialogue, so name that "ContinueButton". Change the text
> to say "Continue >>".

> Resize the panel so that it takes up about 1/2 of the width of your screen and 1/2 of the height. Orient the panel to the middle-bottom of
> the screen, or wherever you want your dialogue to appear. Move the NPCName to the top-left of the panel, with the dialogue in the middle
> of the panel, and the continue button on the bottom-right corner of the panel.

> Create another button on the canvas. Name the button "TestButton". This will be used to start a test conversation. Change the text
> of this button to say "Start Conversation".

> Next, Create three scripts named "Dialogue", "DialogueManager", and "Interactable".

> Create an empty GameObject in the hierarchy and name it "DialogueManager". Drag the DialogueManager script you just created onto it.

### The Dialogue Script

> Open up the Dialogue script in your text editor. Delete the ": Monobehaviour" part of the class declaration (where it says Dialogue : Monobehaviour)
> as the dialogue class is only needed to hold data so it doesn't need to derive from Monobehaviour.

>Add the following data fields in the class: 

```C#
public string name;

[TextArea(3,10)]
public string[] sentences;
```

> The name holds the NPC's name, and the sentences array holds the sentences for that particular bit of dialogue. This will make the data
> easily customiable from within the unity editor. The [TextArea(3,10)] line makes it so that the text stays within 3-10 lines long.

> Finally, add the line [System.Serializable] to make it so that the data can show up in the Unity editor.

### The DialogueManager Script

> Open up the DialogueManager script and delete the Update function. Add
``` C#
using UnityEngine.UI;
```
> to the beginning of the script so we can use Text variables.
>Add the following variables:

```C#
public Text nameText;
public Text dialogueText;

private Queue<string> sentences;
```
> The dialogueText and nameText will correspond to the npcName and dialogue texts in the editor. We are going to use a Queue of strings to hold the
> sentences of the dialogue because a Queue is a first-in-first-out data structure. This means the data we put in will come out in the order
> that we feed it in. This is what we need for a dialogue system.

> At the start we need to initialize the queue so that we can use it. Add the following line to the start function:

```C#
sentences = new Queue<string>();
```

> Next, we need to be able to start a dialogue. Add the following functions to the script:

```C#
public void StartDialogue(Dialogue dialogue)
    {
        Debug.Log("Starting conversation with " + dialogue.name);
        nameText.text = dialogue.name;
        sentences.Clear();

        foreach (string sentence in dialogue.sentences)
        {
            sentences.Enqueue(sentence);
        }

        DisplayNextSentence();
    }
    
public void DisplayNextSentence()
    {
        if (sentences.Count == 0)
        {
            EndDialogue();
            return;
        }

        string sentence = sentences.Dequeue();
        dialogueText.text = sentence;
    }

void EndDialogue()
    {
        Debug.Log("End of Conversation.");
    }
```

> The StartDialogue function takes a Dialogue as an argument. It sets the name of the NPCName text, 
> clears out any dialogue that was there before, adds all of the sentences in the dialogue to the queue, and
> calls the DisplayNextSentence function to display the first sentence.

> The DisplayNextSentence function ends the dialogue if there are no sentences left in the queue. If there are sentences left in the queue,
> it gets the next sentence from the queue and assigns it to the dialogue text.

> At the moment, the EndDialogue function just prints a message to the console to let us know it is finished.

### The Interactable Script
> Open up the Interactable script. Delete the start and update functions. Add the following code in the Interactable class:
```C#
public Dialogue dialogue;

public void TriggerDialogue()
{
    FindObjectOfType<DialogueManager>().StartDialogue(dialogue);
}
```
> This script holds the dialogue that we will use for our test and holds a function to trigger the dialogue.

#### Testing in Unity

> If you go to test this in Unity with the StartConversation button, nothing will happen because we haven't assigned anything yet!

> Click on the dilogueManager object in the hierarchy. The dialogueManager should now have a space for the name and dialogue in the inspector.
> Drag the npcName and dialogue text components into their corresponding places in the inspector.

> Drag the Interactable script onto the testButton.

> Click on the TestButton. In the OnClick area of the inspector, click the + button to add a behaviour and drag in the interactable script on the button.
> In the drop-down list that appears, click Interactable > TriggerButtonDialogue. This will make it so that a dialogue will be started when we click on
> the button. Go into the Interactable script on the button. There should be an area in the inspector where you can enter an NPC name and 
> some dialogue. Write whatever dialogue you want to appear here.

> Click on the continue button. In the area that says "OnClick" in the inspector, hit the + button to add a behaviour and drag the DialogueManager
> gameObject into the space that appears. In the drop-down list, choose DialogueManager > DisplayNextSentence.

> The dialogue should now change depending on what you entered on the Interactable script and should print a message to the console when it is
> finished!

