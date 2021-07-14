---
title: People Picker in Adaptive Cards
description: Describes how to use the People Picker control in Adaptive Cards
localization_priority: Normal
keywords: Adaptive Cards People Picker
ms.topic: reference
author: Rajeshwari-v
ms.author: surbhigupta
---

# People Picker in Adaptive Cards

> [!NOTE]
> This feature is available in [public developer preview](~/resources/dev-preview/developer-preview-intro.md) only.

The People Picker helps users to search and select people. In Adaptive Card, add the People Picker feature as input control, which works across the Adaptive Card surfaces, such as chats, channels, task modules, and tabs. The People Picker supports the following tasks:        

* Selects single or multiple users. 
* Prepopulates the field with selected users.

## Popular scenarios for People Picker in Adaptive Cards

The following table provides different scenarios and People Picker based actions:

|Scenarios|Actions|
|----------|-------------------------|
|Approval-based scenarios| To request, assign and reassign the approval to the intended people based on the requirement.|
|Incident management| To track incidents and notify, assign and reassign the appropriate person for immediate action.| 
| Project management| To assign tickets or bugs to particular users.|
|User lookup| To look for people in the organization.|

## Understand how People Picker works

You can learn how People Picker works in mobile and desktop:

# [Mobile](#tab/mobile)

The iOS and Android mobile clients support People Picker in Adaptive Cards. You can select People Picker on mobile to launch a new experience, from which you can search. The search experience is similar to any other people selection experience in mobile, such as adding a person to a chat or channel.

![People Picker on Mobile](../../assets/images/cards/mobilepp.gif)

# [Desktop](#tab/desktop)

The web and desktop client support People Picker in Adaptive Card. While searching on the web, People Picker involves an inline typing experience.

![People Picker on Desktop](../../assets/images/cards/desktoppp.gif)

The preceding image shows the reassignment scenario in which user A (Robert) receives a ticket for a sprint planning task in a channel and realizes incorrect assignment. Select **Reassign** where the people picker field is prepopulated with name to reassign the task to the intended user. User A removes the name and selects user B (Mona) and user C (Robin) for the task. User A reassigns the task that sends the information back to the bot to update the card and notify the appropriate users.

---

## Implement People Picker

People Picker is implemented as an extension of the [Input.ChoiceSet](https://adaptivecards.io/explorer/Input.ChoiceSet.html) control. The input control includes the following:   

* Dropdown, such as an expanded selection.
* Radio button, such as a single selection.
* Check boxes, such as multiple selections.  

> [!NOTE]
> The `Input.ChoiceSet` control is based on the `style` and `isMultiSelect` properties.  

### Update schema

The following properties are the new additions to the `Input.ChoiceSet` schema to enable a People Picker experience on the card.  

#### Input.ChoiceSet control

|Property |Type |Required |Description |
|----|----|----|----|
|**choices.data** |**Data.Query** |No |Enables dynamic auto complete for different user types, by fetching results from the dataset specified. |

#### Data.Query

|Property |Type |Required |Description |
|--|--|--|--|
|**dataset** |String |Yes |The type of data that must be fetched dynamically.|   

#### dataset
The following predefined values are provided as **dataset** for people picker:   

|dataset|Search Scope
|--|--|
|**graph.microsoft.com/users** |Search all members of the organization.|
|**graph.microsoft.com/users?scope=currentContext** |Search within the members of the current conversation, such as chat or channel in which the particular card is sent.|  

<br> 

> [!NOTE]
> Currently, the search functionality for all the members across the organization is supported in 1:1 chats with bots, Adaptive Card task modules and tabs only. It is not supported in other 1:1 chats, group chats or channels.  

### Example
The code example for creating People Picker with organization search is as follows:

```json 
{
    "type": "AdaptiveCard",
    "body": [
        {
            "type": "TextBlock",
            "size": "Medium",
            "weight": "Bolder",
            "text": "People Picker with Org search enabled"
        },
        {
            "type": "Input.ChoiceSet",
            "choices": [],
            "choices.data": {
                "type": "Data.Query",
                "dataset": "graph.microsoft.com/users"
            },
            "id": "people-picker",
            "isMultiSelect": true
        },
        {
            "type": "ActionSet",
            "actions": [
                {
                    "type": "Action.Submit",
                    "title": "Submit"
                }
            ]
        }
    ],
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "version": "1.2"
}
```  

The following image illustrates People Picker Adaptive Cards with organization search:

![People Picker Org Search](../../assets/images/cards/peoplepicker-org-search.png)

To enable search within a list of conversation members, use the appropriate dataset defined in the [dataset](#dataset) table. `isMultiSelect` property is used to enable the selection of multiple people in the control. It is set to false by default and this setting allows to select one person only.

#### Data Submission
You can `Submit Action` in an Adaptive Card. You can provide the inputs in the card that are sent back to the bot through an `invoke`. The `Submit Action` is similar to other `Input control` in Adaptive Cards. The `invoke` payload consists of a list of input IDs to their corresponding values.  

In People Picker, when a person is selected in the control, the `AAD ID` of the person is the value sent back. The `AAD ID` is a string and uniquely identifies a user in the directory.

The format of the value submitted to the bot depends on the value of the `isMultiSelect` property:

|value of `isMultiSelect`|Format|
|--|--|
|false _(single select)_|The `AAD ID` of the selected user as a string.|
|true _(multi select)_|A string composed of all the `AAD ID`s of the selected users concatenated with a 'comma'.|  

#### Preselection of People
People Picker supports preselection of people in the control, when creating and sending an Adaptive Card.  
`Input.ChoiceSet` supports the `value` property that is used to preselect a person. The format of this `value` property is the same as the submitted value format discussed in data submission section.  
To preselect a person in the control, specify the `AAD ID` of the person as the `value`. To preselect multiple people, such as `isMultiSelect` is `true`, specify a comma-separated string of `AAD ID`s.

#### Static choices
The static choices support scenarios where custom profiles must be inserted into the predefined datasets.
`Input.ChoiceSet` supports specifying `choices` statically in the json. The static choice is used to create the choices from which the user can select.

> [!NOTE]
> Static `choices` are used in with dynamic datasets. 

The choice consists of `title` and `value`. When used along with People Picker, these choices are translated to people profiles that have the `title` as the name and the `value` as the identifier. These custom profiles are also part of the search results when the search query matches the given `title`.   

## See also

[Cards Reference](cards-reference.md)
