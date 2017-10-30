
In this file, we will describe the new format for the chatbot sequences. This format was written in October 2017 and aims at broadening the scope of possible actions in the chatbot, whilst simplying the format and making more flexible

## 1. Scenario

In this following sequence example:
* A chatbot will ask us a question ("Do you like puppies?")
* The chatbot will suggest answers as a set of choices ("Yes" or "No")
* We will select an answer
* The chatbot will react to our answer and ask a second question
* We will answer it using a carousel
* The bot will execute an action as a result of this choice

## 2. Asking a question

To start an interaction with a user, we need three ingredients:
1. a `Type`: this can either be `node` or a `leaf`. A `node` needs to be followed by something ; a `leaf` is an end to the sequence. If you want to allow the user to react, you need to use a `node` (see Section on The Skeleton of a sequence)
2. `Steps` : an array of content defining what you want to send to the user
3. An `Id` for analytical purpose

Example:

     "Type": "Node",
     "Id": "LikePuppies"
     "Steps": [
     {
          "Type": "Image",
          "Id": "10",
          "Source": {
               "Type": "Gif",
               "Source": "Giphy",
               "Path": "RHdoPmZgiJlzW"
               }
          }
     },
     {
          "Type": "Text",
          "Id": "20",
          "Label": {
               "en": "Do you like puppies?",
               "fr": "Est-ce que tu aimes les chiots?"
               }
          }     
     }
     ]
     ...

In this example, we have opened a `node`, defined an `Id` and used `Steps` to fill in the content that we want to show users. In this case, it will first be a `Gif`, followed by `Text` (a question).

**Note**: each `Id` within the `Steps` defines the order of these sequences. In this examples, the `GIF` will be displayed before the `Text` because it has a smaller `Id`

Let's now see how we can offer the user to answer this question.

## 3. Showing choices to the user

To show command choices to the user, we need to use `Command`:
* It store an array of choices that the user can select
* An `Id` will need to be defined for each choice
* `CommandLabel` defines the content of these commands

Example:

     ...
     "Commands": [
     {
          "Type": "node"
          "Id": "LikePuppiesYes"
          "CommandLabel": {
               "en": "yes",
               "fr": "oui"
          }
     },
     {
          "Type": "leaf"
          "Id": "LikePuppiesNo"
          "CommandLabel": {
               "en": "no",
               "fr": "non"
          }
     }
     ]
     
The first command contains the `Type`: `node` - this means it will be followed by something. The second command contains the `Type`: `leaf` - it means the sequence will not have a another user input. We can still however react to the user's previous choice. 


## 4. Reacting to the user's choice

We will want to react to the user's choice. We can do this simply by adding `Steps` to our command.

Example:

     ... 
    "Commands": [
     {
          "Type": "node"
          "Id": "LikePuppiesYes"
          "CommandLabel": {
               "en": "yes",
               "fr": "oui"
          }
     },
     {
          "Type": "leaf"
          "Id": "LikePuppiesNo"
          "CommandLabel": {
               "en": "no",
               "fr": "non"
          },
          "Steps": [
               {
                    "Type": "Image",
                    "Id": "10",
                    "Source": {
                         "Type": "Picture",
                         "Source": "Web",
                         "Path": "https://assets3.thrillist.com/v1/image/2508887/size/tmg-article_tall.jpg"
                         }
                    }
               },
               {
                    "Type": "Text",
                    "Id": "20",
                    "Label": {
                         "en": "I prefer cats as well :-)",
                         "fr": "Je préfère aussi les chats :-)"
                         }
                    }     
               }
          ] 
     }
     ]


Here we react to the user's choice by adding a picture and some text.

## 5. Asking another question

As we've seen, our `Type`: `node` needs to be followed by another `Type`. In this case, we will want to ask another question to the user. To do that, we need to use the connector `LinksTo`.

     ...
     "Commands": [
     {
          "Type": "node"
          "Id": "LikePuppiesYes"
          "CommandLabel": {
               "en": "yes",
               "fr": "oui"
          },
          "LinksTo": {
               "Type": "Node",
               "Id": "PuppiesYesWhichPuppie",
               "Steps": [
                    {
                         "Type": "Text",
                         "Id": "10"
                         "Label": {
                              "en": "Which of these do you prefer?",
                              "fr": "Lequel préfères-tu?"
                         }
                    }
               ]
          }
     },
     ...

## 6. Showing a carousel of choices (picture + text)

Again, we will want to show choices to our user, so we can interact with her/him. 
This time however, we will want to take advantage of the carousel format offered by messaging clients. They are usually made of an image and text.

     ...
     "LinksTo": {
          "Type": "Node",
          "Id": "PuppiesYesWhichPuppie",
          "Steps": [
               {
                    "Type": "Text",
                    "Id": "10"
                    "Label": {
                         "en": "Which of these do you prefer?",
                         "fr": "Lequel préfères-tu?"
                    }
               }
          ],
          "Commands": [
               {
                    "Type": "leaf"
                    "Id": "PreferPuppyLabrador"
                    "CommandLabel": {
                         "en": "Labrador",
                         "fr": "Labrador"
                    },
                    "CommandPicture": {
                         "Type": "Image",
                         "Source": "Web",
                         "Path": "https://www.thelabradorsite.com/wp-content/uploads/2015/07/yellow-labrador-puppy-garden.jpg"
                    }
               },
               {
                    "Type": "leaf"
                    "Id": "PreferPuppyCorgi"
                    "CommandLabel": {
                         "en": "Corgi",
                         "fr": "Corgi"
                    },
                    "CommandPicture": {
                         "Type": "Image",
                         "Source": "Web",
                         "Path": "https://www.thelabradorsite.com/wp-content/uploads/2015/07/yellow-labrador-puppy-garden.jpg"
                    }
               }
               ]
         }
     ...

To create a carousel, simply add the property `CommandPicture` just after `CommandLabel` and fill it with the relevant details. 

## 7. Executing an action

In some cases, we might want to execute an action as a result of this choice. Actions can be defined by the client or external services. 
For instance, we might want to count the number of people who prefered Labradors or Cogis. 

     ...
     "Commands": [
          {
               "Type": "leaf"
               "Id": "PreferPuppyLabrador"
               "CommandLabel": {
                    "en": "Labrador",
                    "fr": "Labrador"
               },
               "CommandPicture": {
                    "Type": "Image",
                    "Source": "Web",
                    "Path": "https://www.thelabradorsite.com/wp-content/uploads/2015/07/yellow-labrador-puppy-garden.jpg"
               },
               "Steps": [
                    {
                         "Type":"Action",
                         "Id":"10",
                         "Name":"DoVote"
                    },
                    {
                         "Type": "Image",
                         "Id": "20"
                         "Source": {
                              "Type": "Gif",
                              "Source": "Giphy",
                              "Path": "6Umkh0GwRYhfG"
                          }
                    }
                    {
                         "Type": "Text",
                         "Id": "30"
                         "Label": {
                              "en": "Sooo cute",
                              "fr": "Tellement mignon"
                         }
                    }
               ]
          },
     ...


Using this specific action Dovote, we can call the relevant API and count the number of user who voted for one choice or the other. There are many more actions - they will be defined in this file [to be created]


## 8. Taking a break

Steps can be very long arrays of content. We may want to show a GIF, text, an image, another text, another GIF and so on. To prevent overwhelming the user with content and space the display of content in time, we can use a `Break` within the Steps.

It will look as follows: 

     ...
     "Steps": [
          {
               "Type":"Action",
               "Id":"10",
               "Name":"DoVote"
          },
          {
               "Type": "Image",
               "Id": "20"
               "Source": {
                    "Type": "Gif",
                    "Source": "Giphy",
                    "Path": "6Umkh0GwRYhfG"
                }
          },
          {
               "Type": "Break",
               "Id": "25",
               "Mode": "Wait",
               "Parameters": {
                   "ms": 4000
                }
          },
          {
               "Type": "Text",
               "Id": "30"
               "Label": {
                    "en": "Sooo cute",
                    "fr": "Tellement mignon"
               }
          }
     ...

Here, we have introduced a break of 4 seconds (or 4000 milliseconds) between the GIF and the text. It means that the client will need to wait 4 seconds after it displays the Gif and before it displays the next step to the user - in this case a text.


## 9. Appendix: further details on the sequences

### 9.1 The skeleton of a sequence

The structure of a sequence is similar to that of a tree: each level is either made of nodes ("`Node`") or leaves ("`Leaf`"). A `node` needs to be followed by something ; a `leaf` is an end to the sequence. 

Example:

     {
     "Type": "Node",
     "Command": {
        "Type": "Leaf"
          },
          {
        "Type": "Node",
        "LinksTo": {
          "Type": "Leaf"
          }
     }
        

Think of them as binary: each sequence step has to have a `Type` ; each `Type` needs to be either a "`Node`" or "`Leaf`"

`Command` and `LinksTo` are used to transition from one `Type` to another. `Command` points to commands that the user will see on his/her screen. 


### 9.2 Breaks, wait and full-stops

#### 9.2.1 Breaks

We have introduced `Break` in Section 8. They are special steps that introduce a delay between 2 other steps. They take the following form:

    ...
      "Steps": [
    {
      "Type": "Break",
      "Id": "40",
      "Mode": "Wait",
      "Parameters": {
        "ms": 4000
      }
    },

#### 9.2.2 Fulls-stops with a client default behaviour

There is also another `Break` mode, that introduce a full-stop. It hs to be inserted in an array of `Steps` and takes the following shape:

    ...
    {
      "Type": "Break",
      "Id": "60",
      "Mode": "Stop"
    },
    ...

In the `Stop` mode, it's on client responsibility to show the right toolbar by default (for example "would you like to continue?" + buttons yes/no). This can be defined in bot resources file for example.

#### 9.2.3 Fulls-stops with a question

However, we can define a specific button within the sequence itself. For example, we can write:

    {
      "Type": "Break",
      "Id": "20",
      "Mode": "Stop",
      "Label" : {
         "en" : "go on?", 
          "fr": "on continue?"
          },
     "Options": {
        "yes" : {  "en" : "yes", "fr": "oui", "es":"si" },
        "no" : {  "en" : "no", "fr": "non", "es":"no" },
     }
    }

In this case, we need:
- `Label` to display a question or text content to the user
- `Options` define the button the users will see and will be able to select

Both of them can be on their own (to introduce a "Next" button for instance).

### 9.3 The order of the Steps

#### 9.3.1 General rule

In the `Steps`, each array contains contain several hashes of content (actions, text, images, gifs). These hashes are ordered using an ID. Example:

     ...
     "Steps": [
         {
               "Type": "Image",
               "Id": "20"
               "Source": {
                    "Type": "Gif",
                    "Source": "Giphy",
                    "Path": "6Umkh0GwRYhfG"
                }
          }
          {
               "Type": "Text",
               "Id": "10"
               "Label": {
                    "en": "Sooo cute"
               }
          }
    

In the steps above, the text will be displayed before the Gif, because its `Id` (10) is smaller than the Gif's `Id` (20), even though they are ordered otherwise. The `Id` defines the order of the Steps.

#### 9.3.2 Randomizing steps 

We can also imagine have several `Steps` having the same `Id`. In this case, we will randomize the steps and display the content in a random order

### 9.4 A catalogue of actions

We can insert an `Action` as part of `Steps`, as explained in the previous example. Actions trigger the use of external resources or service in the bot. We will here list the most popular actions and the way they can be used

#### 9.4.1 Action: Survey

As seen earlier in the example, we can use the voting API by implementing the following `DoVote` action in the steps:

     ...
          {
               "Type":"Action",
               "Id":"10",
               "Name":"DoVote"
          },
     ...
 
Make sure to this action in relation to the relevant API and the right counters. 
Behaviour: when a user selects this command, we should increment the counter associated with this choice.

#### 9.4.2 Action: ShowSurveyResults

The counterpart to the `DoVote` action is the `ShowSurveyResults`. It looks like that: 

     ...
          {
               "Type":"Action",
               "Id":"20",
               "Name":"ShowSurveyResult"
          },
     ...
 
Again, make sure to this action in relation to the relevant API and the right counters. 
Behaviour: when a user selects this command, we should show him/her the number of people who selected this choice. It is therefore a dependency of the `DoVote` action

#### 9.4.3 Action: ShowCards

We can use the action `ShowCards` to show a card made of some text and an image. We can specify the category of the text and the image that we want to receive by detailing the `Intention` in the `Params`
Example:

     ...
       "Steps": [
         {
           "Type": "Action",
           "Id": "10",
           "Name": "ShowCards",
           "Params": {
             "Type": "Intention",
             "Id": "9B2C8B"
           }
         }
       ]
     ...

Make sure the relevant API is connected to this action.

#### 9.4.4 Action: SetUserProperty

In some cases, we might want to remember and set information about a user. For instance, we might want to remember that he or she is single if that is relevant:

     ...
     {
          "Type": "Action",
          "Id": "20",
          "Name": "SetUserProperty",
          "Params": {
            "RelationshipStatus": "Single"
         }
     }
     ...

We can therefore use the property `SetUserProperty` to set a specific param as a result of a user choice. We can store as many `Params` as we want.

### 9.5 Quiz properties for client animation

Some sequences will be identified as Quiz sequences. A Quiz sequence is a normal sequence that contains a question and several choices. Its specificity is that it has a definite right answer and potentially one or several wrong answer(s).

Some clients might want to behave differently whether the answer is right or wrong. For instance, we may want to include an animation embedded in the chatbot's behaviour. To do so, the client needs to know whether the user's choice is actually right or wrong.

We can do it using the following action: 

     ...
       "Type": "Leaf",
       "Id": "FastestDolphin",
       "StepValue": "1",
       "isCorrect": "false",
       "CommandLabel": {
         "en": "dolphin"
       },
       "Steps": [
        {
           "type":"action",
           "id": "1",
           "name": "notifyCorrectness"
        },
         {
           "Type": "Text",
           "Id": "10",
           "Label": {
             "en": "A blue whale can swim as fast as 50km/h, whereas most dolphins don't reach 40km/h"
           }
         }
       ]
     ...
     
In the above example, we are introducing two things:
1. A property `isCorrect` that takes a Boolean `true` or `false` as a value to indicate if the selected choice is correct or not
2. An action `notifyCorrectness` that directs the client to adopt its specific behaviour, whether the answer is right or wrong


### 9.6 Type: Story
