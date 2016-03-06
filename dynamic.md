---
layout: page
title: Dynamic rulebook
toc: true
---

<script type="text/javascript">

function toggleCL() {
  if (readCheckbox('#cylonleader')) { 
    $('#cylonleader').prop('checked', false);
  } else { 
    $('#cylonleader').prop('checked', true);
  }
  flipSwitches();
  return false; 
}

function readCheckbox(id) {
  return $(id).is(':checked')
}

function enable(id) {
  $(id).removeAttr('disabled');
}

function forbidCheckbox(id) {
  $(id).prop('checked', false)
       .prop('disabled', true);
}

function forbidMenu(id) {
  $(id).prop('disabled', true);
  if ( $(id).is(':selected')) {
    $(id).removeAttr('selected');
  }
}

function validateForm() {
  if (readCheckbox('#pegasus')) {
    enable('#newcaprica');
  } else {
    forbidMenu('#newcaprica');
  }
  // Exodus checkboxes only allowed with Exodus.
  if ( readCheckbox('#exodus') ) {
    // Enable those boxes
    enable('#personalgoal');
    enable('#finalfive');
    enable('#cylonfleet');
    enable('#ioniannebula');
  } else {
    // Disable them and also make sure they're not checked.
    forbidCheckbox('#personalgoal');
    forbidCheckbox('#finalfive');
    forbidCheckbox('#cylonfleet');
    forbidMenu('#ioniannebula');
  }
  if ( $('#ioniannebula').is(':selected')
       || ! readCheckbox('#exodus') ) {
    forbidCheckbox('#allyseasons');
  } else {
    enable('#allyseasons');
  }
  
  if (readCheckbox('#daybreak')) {
    enable('#searchforhome');
  } else {
    forbidMenu('#searchforhome');
  }
  if (readCheckbox('#pegasus') || readCheckbox('#daybreak')) {
    enable('#cylonleader');
  } else {
    forbidCheckbox('#cylonleader');
  }
}

function flipSwitches () {
  // Step 1: validate the form. Uncheck and disable items that aren't
  // allowed.
  
  validateForm();
  
  // Step 2: Collect lists of classes to hide and show.
  var showThese = [];
  var hideThese = [];
  $('input,option').each(function(index, element) {
    if ($(this).is(':checked')) {
      showThese.push($(this).attr('id'));
      hideThese.push('no'+$(this).attr('id'));
    } else {
      showThese.push('no'+$(this).attr('id'));
      hideThese.push($(this).attr('id'));
    }
  });  
  
  if (readCheckbox('#daybreak') 
      || readCheckbox('#pegasus')
      || readCheckbox('#exodus')) {
    showThese.push('expansion');
    hideThese.push('noexpansion');
  } else {
    showThese.push('noexpansion');
    hideThese.push('expansion');
  }
  
  if (readCheckbox('#pegasus') || readCheckbox('#exodus')) {
    showThese.push('execution');
    hideThese.push('noexecution');
  } else {
    showThese.push('noexecution');
    hideThese.push('execution');
  }

  
  if (readCheckbox('#daybreak') || readCheckbox('#pegasus')) {
    showThese.push('treachery');
    hideThese.push('notreachery');
  } else {
    showThese.push('notreachery');
    hideThese.push('treachery');
  }
  
  if (readCheckbox('#ioniannebula') || readCheckbox('#allyseasons')) {
    showThese.push('allies');
    hideThese.push('noallies');
  } else {
    showThese.push('noallies');
    hideThese.push('allies');

  }
  
  // Step 3: Show all the classes that need showing.
  for (i in showThese) {
    $('.'+showThese[i]).show();
  }
  // Step 4: Hide all the classes that need hiding. Since we do this 
  // last, that means a given tag needs *all* elements to be visible,
  // or in other words, each list of tags is ANDed together.
  for (i in hideThese) {
    $('.'+hideThese[i]).hide();
  }
  
  // Step 5: Fix the rowspan on the basestar attack table. It has to
  // change based on the options set.
  var rowspan = 3;
  if (readCheckbox('#daybreak')) {
    // Additional one for assault raptors
    rowspan++;
  }
  if ( readCheckbox('#cylonfleet')) {
    // Remove the nuke row
    rowspan--;
  }
  $('#basestardamage').attr('rowspan', rowspan);
  
  // Step 5: Process the "showfirst" class. It needs to show only its
  // first visible child, or nothing at all if none are visible. 
  
  // Step 6: Refresh the table of contents.
  $('#toc').toc({showEffect: 'none'});
  
  // Save to local storage
  save();
}

function save() {
  if (window.sessionStorage){
    $('input,option').each(function(index, element) {
      if (readCheckbox('#'+$(this).attr('id') )) {
        window.sessionStorage.setItem($(this).attr('id'), "1");
      } else {
        window.sessionStorage.removeItem($(this).attr('id'));
      }
    });  
  }
}

function init() {
  if (window.sessionStorage){
    for (id in window.sessionStorage) {
      $('#'+id).prop('checked', true);
      $('#'+id).prop('selected', true);
    }
  }
  $('#configform').change(flipSwitches);
  flipSwitches();
}

//var oldLoad = window.onload;
window.onload = function () {
  //if (oldLoad) {
  //  oldLoad();
  //  $('#toc').toc();
  //}
  init();
};

</script>

<form id="configform">
  <fieldset>
    <legend>Configuration:</legend>
    <label><input type="checkbox" name="pegasus" id="pegasus"> Pegasus</label><br>
    <label><input type="checkbox" name="exodus" id="exodus"> Exodus</label><br>
      <div style="margin-left: 20px" class="exodus">
        <label><input type="checkbox" name="personalgoal" id="personalgoal"> Personal Goal cards</label><br>
        <label><input type="checkbox" name="finalfive" id="finalfive"> Final Five cards</label><br>
        <label><input type="checkbox" name="cylonfleet" id="cylonfleet"> Cylon Fleet board</label><br>
      </div>
    <label><input type="checkbox" name="daybreak" id="daybreak"> Daybreak</label><br>
    <label>Ending:
      <select id="ending">
        <option value="kobol" id="kobol" selected>Kobol</option>
        <option value="newcaprica" id="newcaprica">New Caprica</option>
        <option value="ioniannebula" id="ioniannebula">Ionian Nebula</option>
        <option value="searchforhome" id="searchforhome">Search for Home (Earth)</option>
      </select>
    </label>

    
    <br>
    <label><input type="checkbox" name="cylonleader" id="cylonleader">Cylon Leader chosen</label><br>
    <label><input type="checkbox" name="allyseasons" id="allyseasons">Allies for All Seasons</label>
  </fieldset>
</form>

{% include toc.html %}

## Introduction

This rulebook is intended to be a single resource for the official rules of Battlestar Galactica by Fantasy Flight Games, including its 3 expansions. The goals are for it to be complete and unambiguous, incorporating the published rules included with the games as well as clarifications and rulings made later, so that no one has to dig through 4 rulebooks, errata, official FAQs, unofficial FAQs, and rulings by FFG employees in response to customer questions. 

For beginning players, it's probably best to use the official base game rulebook to learn the game. This rulebook has lots of detail, even when all the expansions are turned off, which is probably going to hurt more than help. But if you are playing the game and have a question that the official rulebook doesn't seem to answer, take a look. An experienced player should be able to use this to teach new players the game, since they can explain the basics themselves and know what details can be ignored to start. 

Configure which expansions and options are being included by using the form at the top of the page. Some rules change based on which expansions or options are included, and the rulebook will change as you configure it. 

While this rulebook sticks mainly to the official rules, it does include some "house rules", and the option to enable a variant by Alexander DeSouza called "Allies for All Seasons" which pulls in the "ally" mechanic without the rest of the Ionian Nebula ending. Whenever possible, this guide treats the base game and expansions as one big game with many different modules and variations, so any other house rules should be easy to integrate and make decisions about. 

## The basics

Humanity has been decimated in a surprise attack by the Cylons, a race of machines who were created by humans and rebelled against them. After 40 years of peace, the remaining members of the human race are suddenly on the run, fleeing through space and fighting for survival. In the escape, however, it becomes clear that the Cylons have evolved -- there are models that are completely indistinguishable from humans, and some don't even *know* that they are Cylons.

During the game, the human fleet will attempt to escape the pursuing Cylons, moving toward the final objective in a series of faster-than-light jumps. The humans have re-formed their government and what's left of the military still follows a chain of command... but can those in authority be trusted?

Each player will select a character and be dealt Loyalty cards. Characters have different skills and abilities, and a well-balanced team of players will help the humans succeed and survive. Players take turns, where they can draw cards, move, and take an action, but at the end of each turn all players must deal with a crisis. It could be a surprise Cylon attack, or a dilemma with no clear answer. All this time, some players are secretly Cylons, or may become Cylons partway through the game, so trust is a critical factor. A hidden Cylon could choose to sabotage at a critical moment, or cause infighting amongst the humans. Cylons can also choose to reveal themselves, causing damage along the way and allowing the Cylon to openly fight the humans. 

## Game setup

### Game board
Setting up the game board is fairly straightforward. The resource dials are set to their defaults, skill cards go next to their respective spaces on the bottom, vipers and raptors go in the reserves, and the fleet marker goes on the jump track. All other components and decks are placed somewhere near the board for use later. The Skill card discard piles are face-up next to *each* Skill draw pile. 

When an expansion includes a "fancy" version of an existing component, such as the plastic basestars and Centurions, they are used *instead of* those components, not *along with*. The number of components is important, because they are [not treated as unlimited resources](#component-limitations). 

<p class="nodaybreak">Even though the Daybreak expansion isn't being used in this game, if you have it, it can be useful to use Miracle tokens to track who has and has not used their once-per-game ability. </p>

<p>The starting layout of ships is:</p>

![See text description below](images/starting_layout.png)

- One basestar and 3 raiders in front of Galactica (left space area)
- Two vipers below Galactica (one in each space area with the launch tube icon)
- Two civilian ships behind Galactica (right space area). 

<div class="pegasus" markdown="1">

### Pegasus

To add the Pegasus expansion, first remove the existing "Investigative Committee" cards from the base game's Politics deck. Pegasus includes a new set of "Investigative Committee" cards that *don't* show the Destiny deck's cards. 

Put the Battlestar Pegasus board to the right of the main board so that the space for the Treachery skill card deck lines up with the other Skill card decks. <span class="nodaybreak">Put the "Cylon Locations" overlay on top of the main board's Cylon Locations.</span><span class="daybreak">Do not use the Pegasus "Cylon Locations" overlay, the Daybreak one will be used instead.</span>

Next, add these components from the Pegasus expansion:

- The new character sheets and tokens
- The Battlestar Pegasus game board and Pegasus damage tokens
- New Skill Cards <span class="nodaybreak">(including "Reckless" Treachery cards)</span><span class="daybreak">(but do not add the Treachery cards, the ones from Daybreak will be used instead)</span>
- New Crisis, Destination, Quorum, Super Crisis Cards, and Loyalty cards
- The "Scar" token, which denotes a special raider

<p class="cylonleader">To play with Cylon Leaders, also add the Cylon Leader sheets and tokens. The Cylon leader will also need the "Infiltrator" card and Agenda cards.</p>

<p class="nocylonleader">These rules are configured for play *without* a Cylon leader.</p>

</div>

<div class="exodus" markdown="1">

### Exodus

Add the following components from Exodus:

- New character sheets and tokens
- New Crisis, Quorum, Super Crisis, Skill, and Destination cards. <span class="nocylonfleet">Do not add Crisis and Super Crisis cards that say "CAG Chooses" on them.</span> 
- One additional nuke token next to the board. The Admiral still only receives 2 to start, but now humans may earn more through game effects. 

<div class="cylonfleet" markdown="1">

In addition, follow these steps to set up the Cylon Fleet module:

- Remove all Cylon attack cards from the Crisis and Super Crisis decks. Also remove the "Mining Asteroid" Destination, if present (since it uses the "Scar" Cylon attack card). 
- Make sure that all Crisis and Super Crisis cards from Exodus were added, including those that say "CAG Chooses".
- Place the Cylon Fleet board to the left of the main board, with the Cylon Pursuit marker on the "Start" space.
- Remove 2 regular vipers from the game, and place the 4 Mark VII vipers in the "Damaged Vipers" box. 
- Add the 4 additional Cylon raiders to the stock of Cylon raiders, for a total of 20. 
- Add the CAG title card with the other title cards for distribution later.
- Replace the Admiral title card with the alternate version from Exodus. It changes how nukes work. <span class="newcaprica">This Admiral card is used together with the New Caprica Admiral card.</span>
</div>

</div>

<div class="daybreak" markdown="1">

### Daybreak

To add the Daybreak expansion:

- Place the Colonial One overlay over Colonial One, and the Cylon Locations overlay over the Cylon Locations. This Cylon Locations overlay is the one with "Hub Destroyed" on the back. Make sure that "Colonial One Destroyed" and "Hub Destroyed" are face down. 
- Place one assault raptor in the Reserves, and the rest next to the game board with the other ships. 
- Shuffle the Mutiny deck and place it next to the board.
- Add the new character sheets and tokens.
- Add in the new Skill cards, including the "Mutiny" Treachery cards, which are a new type. <span class="pegasus">Note that the Daybreak and Pegasus Treachery cards are *not* intended to be mixed. When playing both expansions, use the Daybreak Treachery cards and remove the Pegasus Treachery cards from the game. </span>
- Give each player 1 miracle token. 

</div>

<div class="kobol" markdown="1">
### Kobol ending

The Kobol ending does not require any additional setup. Give the Kobol objective card to the Admiral and follow its instructions when the indicated distances are reached or exceeded.

</div>

<div class="newcaprica" markdown="1">
### New Caprica ending

To play with the New Caprica ending, give the "New Caprica" objective card to the Admiral and follow the instructions on it at the indicated distances. 

<span class="nocylonfleet">The New Caprica President and Admiral title cards add new information that will be used in the New Caprica phase, so they are to be used instead of the regular title cards.</span><span class="cylonfleet">Use the New Caprica President and Admiral title cards instead of the regular versions. The Admiral should take both alternate Admiral cards: one adds a new action for the New Caprica phase, and the other changes the way nukes work. Both of these changes apply in this game.</span>

Have the New Caprica game board, New Caprica Crisis deck, and Occupation Force tokens ready for when the New Caprica phase begins, but it doesn't need to be set up until then. 

</div>

<div class="ioniannebula" markdown="1">
### Ionian Nebula ending

Follow these instructions to set up the Ionian Nebula ending:

- Give the Admiral the Ionian Nebula objective card. Follow its instructions at the indicated distances. 
- Replace the regular basestar damage tokens with the larger, rectangular damage tokens. Remove the old damage tokens from the game.
- Place the trauma tokens facedown and randomize them. 
- Shuffle and set aside the Crossroads Cards. 
- Shuffle the Ally cards and put the Ally tokens next to the board. 

There are more setup instructions for Ionian Nebula after players choose their characters. 

</div>

<div class="searchforhome" markdown="1">
### Search for Home ending

To set up the Search for Home, give the "Earth" Objective card to the Admiral and follow its instructions when the indicated distances are reached. 

Place the Demetrius board to the left of the main game board and leave room for the Rebel Basestar board, which may be set up later as the result of a Mission. The Mission deck is shuffled and placed near Demetrius. 

</div>


### Choosing characters

Determine a first player randomly. They will choose their character first and take the first turn. Both character choice and gameplay will proceed clockwise from there. 

<p class="cylonleader">Only 1 player is allowed to choose a Cylon Leader. Cylon Leaders cannot be chosen in a 3 player game.</p>

Players may not choose "alternate" versions of characters that have already been chosen. Note that "Boomer" and "Athena" are *not* alternates of each other, they are separate characters that may both be chosen in the same game. 

Each character has a category: Political, Military, Pilot, and Support. Support characters may be chosen at any time. The other 3 categories must be chosen evenly. For example, if the first two characters chosen were Political and Military, players may not choose another Political or Military character until a Pilot has been selected. 

Once all characters are chosen, distribute titles to the highest in the orders of succession. 

### Loyalty Deck

![The back of the Loyalty cards](images/loyalty.png)

First, build two decks of Loyalty cards: the "You Are a Cylon" and the "You Are Not a Cylon" decks. <span class="finalfive">The Final Five<span class="personalgoal"> and Personal Goal</span> cards should be added to the "Not a Cylon" deck.</span><span class="personalgoal nofinalfive">The Personal Goal cards should be added to the "Not a Cylon" deck.</span>

These instructions assume that <span class="cylonleader">**one player is playing a Cylon Leader**</span><span class="nocylonleader">**no one is playing as a Cylon Leader**</span>. If this is not the case, <a href="javascript:toggleCL()">click this link</a> or go to the top of the page and change the Cylon Leader option.

Shuffle them both, then form the initial Loyalty deck according to this table:

<div class="nocylonleader noexodus" markdown="1">

| Players |  Cylon cards | Not a Cylon cards | 
| ------- | ------------ | ----------------- |
|    3    |      1       |         5         | 
|    4    |      1       |         6         | 
|    5    |      2       |         8         | 
|    6    |      2       |         9         | 

</div>
<div class="cylonleader noexodus" markdown="1">

| Players |  Cylon cards | Not a Cylon cards | 
| ------- | ------------ | ----------------- |
|    4    |      1       |         5         | 
|    5    |      1       |         7         | 
|    6    |      2       |         8         | 
|    7    |      2       |        10         | 

</div>
<div class="nocylonleader exodus" markdown="1">

| Players |  Cylon cards | Not a Cylon cards | 
| ------- | ------------ | ----------------- |
|    3    |      1       |         6         | 
|    4    |      1       |         7         | 
|    5    |      2       |         9         | 
|    6    |      2       |         10        | 

</div>
<div class="cylonleader exodus" markdown="1">

| Players |  Cylon cards | Not a Cylon cards | 
| ------- | ------------ | ----------------- |
|    4    |      1       |         6         | 
|    5    |      1       |         8         | 
|    6    |      2       |         9         | 
|    7    |      2       |        11         | 

</div>

If either of these characters were chosen, add 1 more "Not a Cylon" card. If both were chosen, add one for each of them.

- Sharon "Boomer" Valerii (Pilot)
- Gaius Baltar (Political)

<p class="daybreak cylonleader">
If playing with 5 or 7 total players, add The Mutineer card to the Loyalty deck.
</p>

<p class="daybreak nocylonleader">
If playing with 4 or 6 total players, add The Mutineer card and one more "Not a Cylon" card to the Loyalty deck. 
</p>

<div class="nodaybreak nocylonleader" markdown="1">

If playing with 4 or 6 players, do *one* of these:

<ul markdown="1">
  <li> Get the "You Are a Sympathizer" card, but don't add it to the Loyalty deck yet. Only add it after the first round of Loyalty cards go out.</li>
  <li class="pegasus"> Get the "You Are a Sympathetic Cylon" card and the "Sympathetic Agenda" cards (they have Caprica Six on the back), but don't add anything to the Loyalty Deck yet. Only add it after the first round of Loyalty cards go out. </li>
  <li markdown="1"> Play with the [rules from the "No Sympathizer" variant](#no-sympathizer). 
  </li>
</ul>
</div>

Keep the "Not a Cylon" deck, taking care to keep it separate from the actual Loyalty deck. It might be used later to add more cards to the Loyalty deck. The unused cards from the "Cylon" deck are now removed from the game without revealing them. 

The Loyalty deck is now complete. Shuffle it and distribute 1 card to every player<span class="cylonleader"> except for the Cylon Leader</span>. After the first round is dealt, add the Sympathizer card to the deck if it was indicated. 

<p class="cylonleader pegasus nodaybreak">
In a 4 or 6 player game, give the Cylon Leader a random Agenda card from the Sympathetic Agenda deck. (This deck has Caprica Six on the back.) In a 5 or 7 player game, give the Cylon Leader a random Agenda card from the Hostile Agenda deck. (This deck has a Centurion on the back.)
</p>

<p class="cylonleader daybreak">
Deal the Cylon Leader two random Motive cards. 
</p>


### First hand of cards

Every player *except* the first player draws 3 cards to start the game. (As always, when a player is drawing Skill cards they must come from within their skill set unless otherwise specified.) The first player will get to draw their normal hand of cards in the first turn. <span class="cylonleader">A Cylon Leader draws 2 cards instead of 3 unless they begin the game as an Infiltrator. </span>

Assemble the Destiny deck by taking 2 of each Skill card type and shuffling it. Whenever the Destiny deck runs out, make a new one in the same way. 

<div class="ioniannebula" markdown="1">
### Ionian Nebula additional setup

To finish setting up for a game with the Ionian Nebula ending:

- Place 1 Trauma token in the Brig, and 1 in Sickbay. Do not look at or reveal either one. 
- Each player draws 3 trauma tokens, keeping them hidden. If a player draws "Disaster", the one that looks like a splatter of blood, they reveal it to everyone, replace it with a new token, then shuffle "Disaster" back into the pool. 
- Draw and place Ally cards and their respective tokens until 3 are placed on the board. If an Ally is a duplicate of an existing player's character, remove it from the game and re-draw. Place a Trauma token from the pool face down on each of the 3 Ally cards. 
</div>

<div class="allyseasons" markdown="1">
### Allies for All Seasons additional setup

Designed by Alexander DeSouza, these rules allow you to incorporate Ally cards and tokens without the rest of the Ionian Nebula endgame. 

To setup for Allies for All Seasons, just before starting the game:

- Remove all “disaster” tokens from the game (the ones that show a blood splatter). The remaining Trauma tokens should be randomized facedown into a pile near the board. 
- Draw and place Ally cards and their respective tokens until 3 are placed on the board. If an Ally is a duplicate of an existing player's character, remove it from the game and re-draw. 
- Each player draws 4 Trauma tokens, and contributes 1 to create the Mood Pool.

Human players want the overall Mood to be benevolent, but also want their own tokens to be benevolent toward the end of the game. Cylons are the exact opposite. 

During the game, any player who is sent to the Brig or Sickbay must also draw a Trauma token. A hidden Cylon who is executed must also draw a Trauma token. 

At distance 8, in addition to whatever the objective card says, [resolve the Trauma phase.](#trauma-phase)

</div>


### Rule reminders

Before starting the game, check that:

<ul>
  <li>The main board was set up with a basestar, 3 raiders, 2 vipers, and 2 civilian ships.</li>
  <li>Each player except the first player drew 3 Skill cards.</li>
  <li class="treachery">2 Treachery cards were included in the Destiny deck. </li>
  <li class="pegasus daybreak">The Pegasus Treachery cards are <em>not</em> being used, just the Daybreak ones.</li>
  <li class="exodus">The Loyalty deck is separate from the "Not a Cylon" deck, and you know which is which!</li>
  <li class="cylonfleet">All of the Cylon Attack cards have been removed from the Crisis and Super Crisis decks, as well as the "Mining Asteroid" destination.</li>
</ul>

It might be a good idea to remind all players of the following:

<ul>
  <li>When choosing a character to send to Sickbay or the Brig, you must choose someone who can actually be moved there if possible.</li>
  <li>Cylon players can choose to ignore effects on Crisis cards.</li>
  <li>Centurions don't "push" each other on the Boarding Party track, more than one can occupy the same space.</li>
  <li>Even if a resource reaches zero, the humans have until the end of the current player's turn to raise it back up before they lose.</li>
  <li>The 10 Skill card limit is only enforced at the end of each player's turn.</li>
  <li>Revealed Cylons should <em>not</em> reveal Loyalty cards that say "immediately reveal this card".</li>
  <li>Super Crisis cards are immune to character abilities and any ability that says it affects Crisis cards.</li>
</ul>

<div class="expansion" markdown="1">

For these expansions, these are some of the important new rules in effect:

<ul>
  <li class="treachery">When a Cylon is revealed, they show <em>one</em> Cylon Loyalty card and pass the rest of theirs face down to the human player of their choice when the distance is 6 or less.</li>
  <li class="treachery">The text on Treachery cards can only be used by Cylon players.</li>
  <li class="pegasus">"Movement:" actions are actions that can be taken instead of moving <em>during the movement step of your turn</em>.</li>
  <li class="pegasus">When Galactica is damaged, the current player may choose to damage Pegasus instead.</li>
  <li class="exodus">When nukes are used, they are discarded rather than removed from the game. The Admiral still starts with only 2 nukes, but they can now be regained (and there's a possible third nuke token).</li>
  <li class="cylonfleet"><a href="#activating-cylon-ships">The rules for activating Cylon ships are different</a> with the Cylon Fleet board.</li>
  <li class="cylonfleet">Civilian ships are <em>not</em> removed from the board after a jump, and all Cylon ships move to the Cylon Fleet board instead of being removed.</li>
  <li class="cylonfleet">When activated, a viper also has the option of escorting a civilian ship off of the board.</li>
  <li class="allies">If you end your movement step in a location containing an ally, you must first encounter it.</li>
  <li class="allyseasons">Whenever a player is sent to the Brig or Sickbay, or executed and revealed as a Cylon, they must draw 1 Trauma token.</li>
  <li class="daybreak">Once-per-game abilities are no longer only once per game. Instead, they can now only be used by spending a Miracle token. Players cannot have more than 1 Miracle token at a time.</li>
  <li class="daybreak">Mutiny cards and the Mutineer are a whole new game mechanic:
    <ul>
      <li class="daybreak">The Mutiny card limit is 1. Players who go over the limit are first sent to the Brig, then must discard down to the limit. Cylon players may not have or use Mutiny cards.</li>
      <li class="daybreak">The Mutineer has a Mutiny card limit of 2 instead of 1. When the Mutineer resolves a Crisis with a "Prepare for Jump" icon, they must draw a Mutiny card.</li>
      <li class="daybreak">If the Mutineer card is still in the Loyalty deck after the Sleeper Agent phase cards are dealt, the current player must choose a human player to deal 1 more Loyalty card to. (If it is the Mutineer card, ignore the instruction to deal them yet another Loyalty card.)</li>
    </ul>
  </li>
  <li class="daybreak">Assault raptors are really more like vipers. They can't be "risked", but they can stay in space during jumps.</li>
  <li class="newcaprica">During and after the New Caprica phase, players may not use Colonial One.</li>
  <li class="searchforhome">The Demetrius is a new ship where Missions can be attempted. A second Mission cannot be attempted until the fleet jumps. If a Mission gains distance, that distance is added immediately. The Skill Checks on Missions are not affected by any cards or character abilities, except limits on how many cards a player can add.</li>
  <li class="searchforhome">The Rebel Basestar is added to the game after a certain Mission is completed.</li>
  <li>Revealed Cylon players must draw 2 cards of different types at the start of their turn.</li>
  <li>When a Cylon uses the Caprica location, the "Prepare for Jump" icon is <em>not</em> ignored.</li>
  <li>The hand of Quorum cards also has a 10 card limit, enforced at the end of each player's turn just like Skill cards.</li>
  <li>Skill cards with the "Skill Check Ability" icon only take effect if they are played into a Skill check and revealed. <span class="pegasus">"Reckless" abilities only take effect if the Skill check was made Reckless by a card.</span></li>
</ul>

</div>


## Playing The Game

### Winning/Losing

The humans win by ending the game without losing. Humans can lose by:

<ul>
  <li>Running out of a resource (0 or less) at the <em>end</em> of a player’s turn or at the end of the game. (Humans can avoid losing if a resource reaches 0 by replenishing that resource before the turn ends.)</li>
  <li>Centurion Invasion: A centurion reaching the “Humans Lose” space on the Boarding Party track</li>
  <li>Galactica Destroyed: Galactica is destroyed when 6 or more locations are damaged at once.</li>
  <li class="execution">No more characters: A human is executed, but there are no more characters to be chosen.</li>
</ul>

### Resolving rule conflicts

This rulebook attempts to be as comprehensive and unambiguous as possible, but Battlestar Galactica is a complex game and individual opinions and interpretations can differ. When trying to resolve ambiguity, try to follow these guidelines:

- Read the text carefully, and try to take it literally. Game text usually means exactly what it says.
- More specific overrides less specific. For example, the text on a Crisis card or character sheet may contradict a normal game rule, and in those cases the game rule is overruled.
- Try to resolve as many effects as possible, and skip any that cannot be resolved. Do all that you can, and ignore anything that you can't. 

### Game turn

A human player's game turn consists of the following steps:

<ol>
<li class="ioniannebula">If there is a <a href="#trauma-tokens">Trauma token</a> in the player's current location, they must draw it and place 1 Trauma token from the pool on that location to replace it.</li>
<li>Receive Skills: Human players receive cards according to <a href="#character-sheets">their character sheet</a>. </li>
<li>Movement: The player may take 1 <a href="#moves">move</a>. <span class="pegasus">They may also instead use a <a href="#movement-actions">Movement action</a>. </span></li>
<li class="allies">At the end of the Movement step, if in a location with 1 or more <a href="#ally-cards">Ally tokens</a>, choose 1 ally to encounter and resolve it. 
    <ul><li>This is simultaneous with Ellen Tigh's "Politically Adroit" since both occur "at the end of your Movement step". As always, the current player (in this case, Ellen Tigh) may choose which order they are resolved in. If encountering an Ally causes a move, she may use "Adroit" in her new location if she hasn't already.
    </li></ul></li>
<li><a href="#actions--abilities">Action</a>: The player may take 1 action.</li>
<li><a href="#crisis-card-resolution">Crisis</a>: Unless otherwise indicated, the current player draws and resolves a Crisis card. </li>
<li>End turn. The next player clockwise will now take a turn.
    <ul><li><em>All players</em> must discard Skill Cards down to the 10 card limit if necessary. </li>
    <li>If any resource is at 0 or lower, humans lose.</li></ul></li>
</ol>


### Component limitations

Obviously there are not an infinite number of ship tokens and other items in the game, so it is possible that they will run out. Follow these rules when a game effect cannot be followed because there are not enough tokens or other pieces.

<p class="nocylonfleet">In the event of component limitations for ships, the current player decides the order of placement, and if necessary, which ones are placed and which are not placed. </p>

<p class="cylonfleet">When using the Cylon Fleet board and a game effect instructs a player to place Cylon ships on the main game board, draw from the normal pile as usual. However, if the normal pile has run out but the Cylon Fleet board has some of the appropriate ship, then ships are drawn from the *lowest numbered* Cylon space area possible. This does not affect the other ships in that space area, and the pursuit track is not advanced. If both the normal pile and the Cylon Fleet board are out of a ship, then the current player decides the order of placement and, if necessary, which ones are placed and which are not placed.</p>

If a heavy raider cannot become a Centurion due to component limitations, the heavy raider remains on the board. 

When instructed to "draw and destroy a civilian ship", [there are special rules that apply when the normal stockpile has run out](#drawing-and-destroying).

When a pilot uses the Hanger Deck, if there are no vipers in the reserves then the pilot may return an unmanned viper in a space area to the reserves and then launch using it. 

When a deck runs out of cards, shuffle its discard pile to create a new deck. <span class="noallies">The only exception to this rule is the Loyalty deck, which is not shuffled or used again after it is depleted.</span><span class="allies">The only exceptions to this rule are the Loyalty deck and the Ally deck, both of which do not get shuffled or used again after they are depleted.</span> In the rare event that the "Not a Cylon" deck is depleted, it should be re-shuffled with all the discarded "Not a Cylon" cards. 

### Die rolls

Various events during the game depend on the results of a die roll. Normally, when a player initiates an action that requires a die roll, that player performs the roll. This is important for characters with the ability to affect "their" die rolls, like Kat. Be careful to check whether an ability specifies "on your turn" as well. 

The rulebook states that when activating the "FTL Control" location, the *current player* rolls to see if population is lost, not the player using the location, making it an exception to this rule. 

Die rolls can be changed by game effects, but the result cannot be made greater than 8 or less than 1. Effects that modify die rolls do *not* apply when a die roll is skipped, such as Kat's "Hot Shot" ability.

### Timing

This rulebook is very specific about the separate phases of resolving various cards and events, so if two players wish to use "interrupting" abilities at the same time, refer to the appropriate procedure. There may be guidance about which ability occurs first, or it may indicate that the two abilities actually occur in different steps. Skill Checks, in particular, have a [surprising number of steps](#skill-check-resolution) where different abilities can apply. 

Otherwise, in general when a timing conflict occurs or a choice needs to be made about what order things happen in, the choice is up to the current player. For example, if two players want to use abilities before a Skill Check, the current player chooses which one happens first. The rejected ability is either withdrawn or that player may still choose to use it after the first is resolved. If the rejected ability was on a card being played, the player takes the card back. 

Players must be given a reasonable opportunity to use abilities. For example, you may not rush to put cards into a Skill check to prevent players from using a card that can only be played before cards are added. 

The current player also chooses which components to place and which to skip if there is [a shortage](#component-limitations), and which space areas are activated first when activating Cylon ships. 

When "all players" are instructed to do something, unless otherwise specified the current player goes first, moving in clockwise order. 

### Actions & Abilities

Cards, character sheets, and locations have various actions and abilities that are available to players to use. Players may play cards to use their abilities, use abilities on their own character sheets, and use the action for their current location. A pilot may also use an action to activate the viper that they are piloting. 

An action will be indicated by the word "Action:", and can be used only when a player is granted an action. Other abilities indicate when they can be played and the effect that they will have. 

<div class="pegasus" markdown="1">
#### Movement actions

A "Movement:" action is a special type of action which can be used by players *only* during the Movement step of *their* turn instead of moving their character. Any game effects that restrict or modify a type of action, like "may not use actions on Piloting Cards" or "discard 1 Skill card to use a Skill card action", also apply to Movement actions. 

The target of an "Executive Order" cannot use a Movement action because it is not their turn, and they are granted a [*move*, not a Movement](#moves-versus-movement-actions). 

Movement actions may be played while in the Brig, since players in the Brig still have a Movement step (they are just prevented from using it to *move*.)

</div>

### Moves

A player who is granted a "move" may move their character token to a new location. A move to a location on the same ship can be performed freely. There is no die roll or number of "spaces" that tokens have to move, the token simply moves directly from one location to another. 

Using a move to go to a location on another ship requires the player to discard a Skill card. If a player has no Skill cards, they will not be able to move themselves to locations off of their current ship. The destination must be selected before the discard occurs, and the player's token is not moved until the discard is fully resolved. 

Players piloting a viper may move to an adjacent space area when granted a move. They may also discard a Skill card to move to a location on any ship and return their viper to the Reserves. Neither of these counts as a viper activation. 

Players may not choose to move to "hazardous" locations, they can only be sent to them by game effects that specify that exact location. Human players are not allowed to move to Cylon locations and vice versa. 

<div class="pegasus" markdown="1">

#### "Sabotage" Treachery card interrupt

Since the "Sabotage" Treachery card interrupts a discard and damages Galactica, it interacts with moves between ships. The damage occurs during the discard, before the player's token has left its starting location (since, as stated above, the discard is fully resolved before moving). 

If the starting location is damaged, the character is instead sent to Sick Bay since they haven't left yet. If the destination is damaged, the token moves to the destination normally. If the destination was on Pegasus, but the damaged destroyed Pegasus, the move is no longer legal so it is ended with the player remaining in their starting location. The moving player cannot change their destination after "Sabotage" is played. 

#### Moves versus Movement actions

There is a subtle difference between a "move" and a "Movement:" action. A "move" is the act of moving a character token, whether that is within a ship, across ships (discarding 1 Skill card), piloting a viper to a new space area, or landing a viper (discarding 1 Skill card). When a card allows a player to move, or says that a player cannot move, these are what it is referring to. In contrast, a Movement action is an action that players may take *only during the Movement step of their turn* instead of moving. If an effect grants a "move", like Executive Order, this does not allow the player to use a Movement action instead. Similarly, although the Brig forbids *moving*, players may still use Movement actions during their Movement step while in the Brig.         

</div>

### Skill Cards

![The back of a Skill card](images/skill.png)

Skill cards have a type, a strength, and an ability. At the end of any player's turn, a player with more than 10 Skill Cards must discard down to 10. When discarded, Skill Cards go face up next to their respective deck. When a deck runs out, shuffle the discard pile to create a new deck. 

Cylon players are not allowed to use the text abilities on Skill Cards<span class="treachery">, with the exception of Treachery cards. Humans are the exact opposite: they may use the text abilities on all Skill Cards *except* Treachery cards. Unless otherwise specified, Treachery cards always count as negative when totaling Skill checks</span>.

#### Skill Check Abilities

When the "Skill Check Ability" icon appears next to the strength of a Skill card, the text of the card is triggered only when it is revealed as a part of a Skill Check. Additional copies of the same card are not resolved. Since Skill Check Abilities are played anonymously, both human and Cylon players can contribute them to Skill Checks<span class="treachery">whether they are Treachery or non-Treachery</span>. 

<div class="pegasus" markdown="1">

#### Reckless cards and abilities

Some Skill cards have the word "Reckless" printed at the top of the text area. They are always abilities that apply at the beginning of a Skill check. When a Reckless ability is used, it makes the subsequent Skill check a Reckless one. <span class="nodaybreak">Some Treachery cards have "Reckless Skill Check" abilities. These function like regular Skill Check Abilities, but are resolved only if the Skill check was made Reckless.</span><span class="daybreak">A Reckless Skill check has a chance of triggering additional Treachery cards. </span>

Only one Reckless card may be played per Skill check. "Restore Order" cannot be played on a Skill check after it has been made Reckless, and Reckless cards cannot be played on a Skill check after it has had "Restore Order" applied to it. 

</div>

#### Types
<ul>
<li>Politics: This skill represents a character’s ability to control morale and help the fleet overcome crises. This is the most common skill type required by Crisis Cards. Some politics cards also provide the ability to allow a player draw Skill Cards from outside his skill set.</li><li>Leadership: This skill represents a character’s ability to take charge of situations and command other individuals. This is the second-most common skill type required by Crisis Cards. Some leadership cards allow players to move other characters and grant them a bonus action.</li><li>Tactics: This skill type represents a character’s ability to plan missions as well as to physically overcome obstacles. Tactics cards allow players to gain bonuses to die rolls as well as scout the galaxy for new destinations.</li><li>Piloting: This skill represents a character’s ability to fly vipers. Piloting cards allow players to reroll enemy attack rolls as well as gain additional attacks.</li><li>Engineering: This skill represents a character’s strength in the mechanical and scientific fields. Some engineering cards allow players to repair vipers and locations aboard Galactica.</li>
<li class="treachery">Treachery: This skill represents nefarious and underhanded tactics. Unlike other Skill types, it is primarily used by Cylon players. </li>
</ul>

#### Destiny Deck

To create the Destiny deck, take two of each type of Skill Card <span class="treachery">(including Treachery)</span> and shuffle. These are used in each Skill Check to add uncertainty and plausible deniability for hidden Cylons. After the last card is used from the Destiny deck, form a new one.

### Crisis card resolution

1. Draw a Crisis card.
2. Reveal the Crisis card and read it.
   - Starbuck's "Secret Destiny" takes place before Baltar's "Delusional Intuition". (Baltar's text should really say "after revealing".)
3. Resolve the Crisis based on whether it is <span class="nocylonfleet">[a Cylon attack](#cylon-attack-cards), </span> [a Skill Check](#skill-check-resolution), or an [event](#event-crisis-card). 
4. [Activate Cylon Ships](#activating-cylon-ships) as directed by the bottom left corner of the Crisis card.
5. If the "Prepare for Jump" icon is present in the bottom right corner, advance the fleet token 1 space. This may trigger [a jump](#jumping-the-fleet). 
6. Discard the Crisis card unless otherwise indicated. 


<div class="nocylonfleet" markdown="1">
### Cylon Attack cards

![A sample Cylon Attack card](images/cac_example.png)


Each Cylon Attack Crisis has 3 steps:

1. Activate: Fully resolve the indicated Cylon ship activation before adding new ships to the board.
2. Setup: Place new ships on the board as shown in the image. The text lists the exact numbers. 
3. Special Rule: The given special rule now applies. If the rule does not say to "keep this card in play", the Crisis card is discarded afterward.   

The ships shown are *new* ships to be placed. Any existing ships on the board stay where they are when performing "Setup". If there are not enough ships, follow [the component limitation rules](#component-limitations). Abilities that occur when a ship is placed, such as Apollo's "Alert Viper Pilot", may take place immediately after the setup phase is complete.

</div>

### Event Crisis cards

![A sample event Crisis card](images/event_example.png)


An "Event" Crisis card has a bar below the picture that says "[Someone] Chooses", with a specific player or title named, as well as an "or" bar between the two choices. 

The indicated player must choose which box to resolve before play can continue. In some cases, both boxes simply have text to be resolved. In others, one of the choices is to attempt a [Skill check](#skill-check-resolution). 

When making a choice, unless specifically stated otherwise a player may select which box gets resolved even if they know that they will not be able to actually fulfill that option. For example, if the choice is between "lose 1 morale" and "the President discards 5 Skill cards", the second option can be chosen even if the President has no Skill cards. However, remember that [restrictions apply to which players can be sent to the Brig or Sickbay](#choosing-players-for-sickbaybrig). 

Two characters have abilities that affect Event Crisis cards in different ways. Lee Adama's "Choose a Different Path" allows him to treat the choice as if there is another box that says "The current player discards 5 Skill cards". Karl "Helo" Agathon's "Moral Compass" allows him to change a choice after it has been made. If both characters are trying to use these abilities, Helo's wins out because while Lee's ability allows him to add a new box that he *may* choose, Helo's allows him to *force* a different box to be chosen after. 

### Super Crisis Cards

![The back of a Super Crisis card](images/supercrisis.png)


Abilities that apply to Crisis cards do not apply to Super Crisis cards. Super Crisis cards are also immune to all character abilities affecting Skill checks. Otherwise, they are resolved using [the same procedure as Crisis cards](#crisis-card-resolution).

### Skill Check resolution

![A regular Skill Check card](images/skillcheck.png)

Each Skill check has a strength in the top left corner, a title, a picture, and colors which count as positive. Any color not "lit up" is negative, and will subtract from the positive cards. The result of the Skill check will determine which of the boxes at the bottom will take effect. A Skill check is passed if it meets or exceeds the strength in the top left corner. There may be a "partial pass" result if the strength reaches a certain threshold but doesn't fully pass, but otherwise the Skill check fails. 

<div class="expansion" markdown="1">
![The Skill Check Ability icon](images/skillcheckability.png)

If a card is revealed in a Skill check with the Skill Check Ability icon, resolve the card's text. If multiple cards with the same name appear, only one is resolved and the rest are skipped. If multiple Skill Check Abilities are present, the current player decides what order they are resolved in. 

</div>

The full, detailed procedure for resolving a Skill check is as follows:

<ol>
  <li>The Skill check begins.
    <ul>
      <li>Resolve any abilities that cause the entire check to pass or fail without any attempt required, such as “Political Prowess” or Boomer’s “Mysterious Intuition”</li>
    </ul>
  </li>
  <li>The “pre-card” abilities can now occur, with the exact order chosen by the current player if multiple cards are played at once.
    <ul>
      <li>This includes abilities with text that says:
        <ul>
          <li>“before making a skill check”</li>
          <li>“before cards are added to a Skill check”</li>
          <li>“when a player activates [some location]”</li>
        </ul>
      </li>
      <li>When the current player chooses an effect to go first, the rejected effect can be withdrawn. If it was on a card, the owner takes the card back.</li>
      <li class="pegasus">Only one Reckless card may be applied to a Skill Check. Reckless cards cannot be played after “Restore Order” has been applied.</li>
      <li class="pegasus">“Restore Order” cannot be played after a Reckless card has been applied.</li>
      <li class="pegasus">When Dee uses her “Fast Learner” to add cards to the Skill check, they aren’t <em>officially</em> added until after the Destiny deck’s cards. This means that more “pre-card” effects can be played even after “Fast Learner” has been used.</li>
      <li class="pegasus">Players can receive new cards from “Support the People” and use them as additional “pre-card” effects as well.</li>
    </ul>
  </li>
  <li>Add cards from the Destiny deck<span class="pegasus"> (followed by Dee’s “Fast Learner” cards if any)</span></li>
  <li>Starting to the left of the current player and proceeding clockwise, each player plays cards face down into the Skill check.
    <ul>
      <li>Unless otherwise specified, a player may play <em>any number</em> of Skill cards into the check.</li>
    </ul>
  </li>
  <li>Shuffle the Skill check cards</li>
  <li>Reveal the Skill check cards
    <ul>
      <li>Caprica Six’s “Human Delusion” occurs before Cally’s “Quick Fix” if both are attempted</li>
    </ul>
  </li>
  <li class="expansion">Resolve Skill Check Abilities, with the order being chosen by the current player.
    <ul>
      <li class="pegasus nodaybreak">“Reckless Skill Check” abilities only take effect if the Skill Check was made Reckless</li>
      <li>The effects from cards with the same name are not resolved more than once during this step. Any subsequent duplicates are ignored.</li>
      <li>If a card is removed after its effect has taken place, the effect still remains. (For example, if “All Hands on Deck” is resolved but removed later, all 0 strength cards still count as strength 1 later on.)</li>
      <li>“Red Tape” and “Quick Thinking” refer to the printed value on the card only, not values after any modification.</li>
    </ul>
  </li>
  <li class="pegasus daybreak">If the Skill Check was made Reckless, do the following:
    <ol>
      <li>Draw 1 Treachery card</li>
      <li>If it is strength 0:
        <ul>
          <li>Draw 1 more Treachery card</li>
          <li>Resolve both Skill Check Abilities.
            <ul>
              <li>Note that the “cards with the same name” rule no longer applies, so neither card should be ignored or skipped as a duplicate.</li>
            </ul>
          </li>
          <li>Remove both cards from the check and discard them. They do not count toward the Skill check’s strength.</li>
        </ul>
      </li>
      <li>If it is not strength 0, it is discarded with no effects.</li>
    </ol>
  </li>
  <li class="expansion">Trigger Consequences, if any
    <ul>
      <li>Consequences are resolved if at “least 1 Skill Card with a Skill Check Ability icon was played into the check”. They are indicated by a “Skill Check Ability” icon on the Crisis card, similar to the “Pass” and “Fail” results.</li>
    </ul>
  </li>
  <li>Total the net strength of the cards (positive minus negative), taking into account any effects that modified them</li>
  <li>Determine outcome: Pass, partial pass, or fail
    <ul>
      <li>“A Second Chance” now applies if it was played</li>
      <li>Abilities that change the amount required to pass a Skill check do not change the amount required for a “partial pass”.</li>
    </ul>
  </li>
  <li>Resolve the outcome’s effects
    <ul>
      <li>“Install Upgrades” now applies if it was played</li>
    </ul>
  </li>
  <li>Discard Skill cards into their appropriate discard piles
    <ul>
      <li>William Adama may override this with “Command Authority”</li>
    </ul>
  </li>
</ol>


### Activating Cylon ships

Appropriately, Cylon ships follow certain programming when activated. Cylon players don't pilot or otherwise control these ships directly. 

When Cylon ships are activated, they are resolved one entire space area at a time. When multiple space areas are affected, the current player chooses what order they are resolved in (and sometimes this makes a difference!). If a Cylon ship moves to another space area that hasn't been resolved yet, it isn't activated a second time. Make sure to keep it separate from the ships that have not been activated yet. 

When resolving a result with multiple Cylon activations, resolve them in left to right order. 

If there are not enough tokens to place ships on the board, the current player chooses which to place, and the rest are skipped due to [component limitations](#component-limitations). <span class="cylonfleet">When the Cylon Fleet board is present and a ship must be placed on the main board, first draw from the regular reserves if possible, then draw from the Cylon Fleet board starting in the lowest numbered space. This does not move other ships in the space area and does not increase the Pursuit track. </span>

When these icons appear on a Crisis card, do the following:

- Activate raiders: [Each Cylon raider is activated once](#activating-a-raider). If there are no raiders on the board, each basestar launches two new raiders. If there are no raiders and no basestars on the board, <span class="cylonfleet">place a ship on the Cylon Fleet board</span><span class="nocylonfleet">nothing happens</span>. 
- Launch raiders: Each basestar launches 3 raiders. If there are no basestars, <span class="cylonfleet">place a ship on the Cylon Fleet board</span><span class="nocylonfleet">nothing happens</span>.
- Activate heavy raiders and Centurions: Follow [the procedure for activating heavy raiders and Centurions](#activating-heavy-raiders-and-centurions). If there are no basestars, centurions, or heavy raiders, <span class="cylonfleet">place a ship on the Cylon Fleet board</span><span class="nocylonfleet">nothing happens</span>. 
- Activate basestars: Each basestar [attacks Galactica](#combat-ship-attack-table). If there are no basestars, <span class="cylonfleet">place a ship on the Cylon Fleet board</span><span class="nocylonfleet">nothing happens</span>. 

<div class="cylonfleet" markdown="1">

To place a ship on the Cylon Fleet board:

1. Note the appropriate ship based on the Activate Cylon Ships icon:
    - Activate Raiders: 1 raider
    - Activate Heavy Raiders/Centurions: 1 heavy raider
    - Launch Raiders or Activate Basestars: 1 basestar
2. If there is an appropriate ship in the reserves, then roll a die and place that ship in the Cylon space area with that number.
    - This die roll **may** be affected by game effects or abilities. 
3. Otherwise, if all ships of the appropriate type are already on the main board or the Cylon Fleet board:
    - Find the highest numbered Cylon space area containing the appropriate ship, and move *all* ships from that space area to the corresponding area on the main board.
    - If the appropriate ship is not on the Cylon Fleet board at all, no ships move over.
4. Finally, advance the Cylon pursuit marker one space. 
    - If it has reached a space with civilian ships underneath it, the CAG must place the indicated number of civilian ships on the main board. 
    - If it has reached "Auto Attack", move *all* ships in Cylon space areas to their corresponding main space areas and reset the Cylon pursuit marker. 

</div>


#### Activating a raider
When an individual Cylon raider is activated, it does the *first available* action in this list and then stops:

1. Attack an unmanned viper in its space area
1. Attack a manned viper in its space area
1. Destroy a civilian ship in its space area
1. Move one space area closer to the nearest civilian ship. If two such space areas are equal in distance, the raider moves clockwise. 
1. If there are no civilian ships, the raider attacks Galactica. 

#### Activating heavy raiders and Centurions
When heavy raiders are activated, do *all* of the following steps: 

- Move all centurions on the board one 1 space further on the Boarding Party track
- If a heavy raider is already in a space area with a launch icon, it is removed from the board and replaced with a Centurion on the start of the Boarding Party track (if there are not enough Centurion tokens, leave the heavy raider in space). 
- Move all heavy raiders on the board one space area closer to the nearest viper launch icon
- If there were no heavy raiders on the board to start, launch one from each basestar. 

When something says to "activate heavy raiders", that *always* includes Centurions as well unless otherwise stated. If something does say to activate heavy raiders but not Centurions, skip the first step but do all of the rest. 

Multiple Centurions can occupy the same space at the same time, they do not "push" each other forward. 

If a heavy raider would normally enter a viper launch tube, but there are no Centurion tokens, it stays on the board since it cannot be converted to a Centurion. As always, the current player decides which order to resolve them in. 

### Jumping the fleet

The human fleet normally jumps by either activating the "FTL Control" location on Galactica, or having the fleet token reach "Auto-Jump". 

When "FTL Control" is used to jump, the *current player* must first roll a die, and on a 6 or lower, the population indicated by the current Jump Preparation space is lost (either -3 or -1). After this is finished, the jump can begin. 

The procedure for jumping is:

<ol>
  <li class="searchforhome">Follow the instructions on the Active Mission space.</li>
  <li class="cylonfleet">Move all Cylon ships to the respective space area on the Cylon Fleet board. When moving a damaged basestar, discard any damage tokens attached to it. Centurions remain where they are on the Boarding Party track.</li>
  <li class="cylonfleet">Return all human <em>combat</em> ships to the Reserves, but leave all civilian ships in place. Pilots who were flying ships move to the Hangar Deck. <span class="daybreak">The current player may choose to leave unmanned assault raptors in space, and pilots of assault raptors may choose to remain in space.</span></li>
  <li class="nocylonfleet">All ships on the game board are removed and returned to their respective pile, the Reserves, etc. unless otherwise specified. Pilots who were flying ships move to the Hanger Deck. Centurions are <em>not</em> removed. <span class="daybreak">The current player may choose to leave unmanned assault raptors in space, and pilots of assault raptors may choose to remain in space.</span></li>
  <li>The Admiral draws 2 Destination cards and, without discussing or showing them, chooses 1 as the next destination for the fleet. The other Destination card is placed at the bottom of the Destination deck and remains secret.</li>
  <li>Follow the instructions on the chosen Destination card.</li>
  <li>If the Objective card specifies something that happens at this new distance, resolve that event completely.</li>
  <li>Reset the Jump Preparation track to the start.
    <ul class="ioniannebula">
      <li>If the “Crossroads” phase was just resolved, skip this step.</li>
    </ul>
  </li>
</ol>


### Sleeper Agent phase

At the Sleeper Agent phase, more Loyalty cards are dealt to every player<span class="cylonleader"> except for Cylon Leaders</span>. It begins when the distance specified on the Objective card is reached. 

Upon reaching this distance or more, deal 1 Loyalty card to every player<span class="cylonleader"> who is not a Cylon Leader</span>, including revealed Cylon players.  Revealed Cylons must ignore any text that says to "immediately reveal this card". <span class="daybreak cylonleader">Deal 2 more Motive cards to the Cylon Leader.</span>

<p class="daybreak">When playing with the "You Are The Mutineer" card, if no one has revealed as the Mutineer after all the Sleeper Agent phase cards have been dealt, the current player chooses a human player to draw an extra Loyalty card from the Loyalty deck. In this case, if they subsequently reveal the Mutineer card, they do not replace it with another Loyalty card. </p>

#### Revealed Cylons during the Sleeper Agent phase

<p class="pegasus">Revealed Cylon players who receive Loyalty cards must look at them, then immediately pass them face down to *one* human player of their choice. They must not follow any text that says to "immediately reveal this card". The human player who receives these cards is the one who will reveal them.</p>

<p class="nopegasus daybreak">Revealed Cylon players who receive Loyalty cards must look at them, then immediately pass them face down to *one* human player of their choice. They must not follow any text that says to "immediately reveal this card". The human player who receives these cards is the one who will reveal them.</p>

<p class="nopegasus nodaybreak">Revealed Cylons get their extra Loyalty card, but do not automatically pass them off, nor do they reveal them even if it says to "immediately reveal this card". Instead, they can choose to pass them off as an action in the Resurrection Ship when the game resumes.</p>

<p class="exodus">Sharon "Boomer" Valerii Valerii normally receives 2 Loyalty cards in the Sleeper Agent phase instead of 1, as per her negative ability. If "Boomer" has already revealed as a Cylon, however, her negative ability no longer applies because character abilities are ignored as a revealed Cylon. Only deal her 1 Loyalty card as normal if she has revealed as a Cylon.</p>

<p class="noexodus">Sharon "Boomer" Valerii Valerii normally receives 2 Loyalty cards in the Sleeper Agent phase instead of 1, as per her negative ability. If "Boomer" has already revealed as a Cylon, however, her negative ability is supposed to no longer apply, because character abilities are ignored as a revealed Cylon. Since following this rule would result in a Loyalty card not being dealt, deal the extra card to "Boomer" despite technically no longer having that weakness so that there is not a card left over.</p>

### Combat ship attack table 

<table>
	<tr><th> Ship being attacked</th> <th> Result </th></tr>
	<tr><td> Cylon raider              </td> <td> 3–8: Destroyed</td></tr>
	<tr><td> Heavy raider or Centurion </td> <td> 7–8: Destroyed</td></tr>
	<tr><td rowspan="3" id="basestardamage"> Basestar      </td> 
	                         <td>With viper: 8 to damage           </td></tr>
	    <tr class="daybreak"><td>With assualt raptor: 7–8 to damage</td></tr>
	                     <tr><td>With Galactica: 5-8 to damage     </td></tr> 
	<tr class="nocylonfleet"><td>With a nuke:	                             <ul>
	                               <li>1–2: Damaged twice</li>
	                               <li>3–6: Destroyed</li>
	                               <li>7–8: Destroyed, and also destroy 3
	                                   raiders in the same space area</li>
	                             </ul>                             </td></tr>
	<tr><td> Viper                   </td> <td> 
	                             <ul>
	                               <li> 5–7: Damaged </li>
	                               <li> 8: Destroyed </li>
	                             </ul>
	                                                               </td></tr>
	<tr class="cylonfleet"><td> Viper mk VII            </td> <td> 
	                             <ul>
	                               <li> 6–7: Damaged </li>
	                               <li> 8: Destroyed </li>
	                             </ul>
	                                                               </td></tr>
	<tr class="daybreak"><td> Assault raptor          </td> <td> 7–8: Destroyed </td></tr>
   <tr><td rowspan="2"> Battlestar Galactica    </td> 
                              <td> With raider: 8 to damage       </td></tr>
                          <tr><td> With basestar: 4–8 to damage   </td></tr>

</table>

<div class="cylonfleet" markdown="1">

With the Cylon Fleet board, a nuke targets an entire space area instead of a single basestar:

- 1–2: Damage a basestar twice
- 3–6: Destroy a basestar
- 7: Destroy a basestar and 3 raiders
- 8: Destroy every ship in the space area

</div>

When a human combat ship is destroyed, it is removed from the game. If it was piloted, that character is moved to Sickbay. Cylon ships are never permanently destroyed, they are always returned to the pile next to the board. 

#### Basestar damage

Basestars are destroyed after taking 3 damage tokens. When a basestar is damaged, draw a token. Each token has some sort of effect:


<ul>
  <li>The “2” is a Critical Hit, which counts for 2 damage tokens instead of just 1.</li>
  <li>The raider is a Disabled Hanger, which prevents that basestar from launching raiders <em>or</em> heavy raiders.</li>
  <li>The 3 missiles firing is Disabled Weapons, which prevents the basestar from firing on Galactica.</li>
  <li>The image of a basestar is Structural Damage, which adds 2 to the die roll for an attack against that basestar.</li>
  <li class="ioniannebula">The 3 raiders with a burst behind them is Collateral Damage, which destroys up to 3 raiders in the same space area as the damaged basestar when it is drawn.</li>
  <li class="ioniannebula">The two hexagons with pictures of the Galactica memorial wall is Damage to Personnel, which forces each Cylon player to draw 2 trauma tokens when it is drawn.</li>
</ul>

If a game effect instructs you to "destroy a basestar", it is destroyed without drawing damage tokens. <span class="noioniannebula">Additionally, do not draw a damage token if a basestar with 2 damage is damaged again.</span><span class="ioniannebula">A basestar with 2 damage tokens that is damaged again should have another token drawn before destroying it, since some types of damage have effects outside of the basestar itself.</span>

#### Damaging Galactica

Galactica is the main military ship in the fleet. It is destroyed when it has 6 damage tokens on it. If Galactica is destroyed, the humans lose. 

When Galactica is damaged, draw a Galactica damage token randomly and reveal it. If it shows a resource icon, lose 1 of that resource and remove that damage token from the game. If it shows an image of a location, that location is damaged. When a location is damaged, any characters in that location at that time are sent to Sickbay. Leave the token on that location to indicate that it is damaged. Afterward, the damaged location can be moved to as normal, but its action cannot be used until the location is repaired. When it is repaired, the damage token is shuffled back into the damage token pile.

<div class="pegasus" markdown="1">

#### Damaging Pegasus

When Galactica is damaged, the current player may choose to damage Pegasus instead. Pegasus has damage tokens as well, which behave the same as Galactica's. There are no "resource" icons on Pegasus damage tokens, just Pegasus locations. Pegasus is destroyed if all 4 locations on it are damaged. Any characters on board Pegasus when it is destroyed are sent to Sickbay, and the Pegasus board is removed from the game. 

#### Scar

Scar is a special raider. He can only be destroyed as the result of a die roll of 7 or 8. Modifiers to die rolls are allowed, so a roll of 6 with "Strategic Planning" would destroy Scar. If a nuke allows raiders to be destroyed, he can be chosen only if the die roll was a 7 or 8. If a game effect allows you to destroy a raider without a die roll, Scar cannot be chosen. If a game effect includes a roll to see "how many" raiders to destroy, Scar can be chosen only if that roll was a 7 or an 8. Kat's "Hot Shot" ability skips the die roll entirely, and so cannot be used to destroy Scar. 

</div>


### Human combat ships

#### Civilian ships

Civilian ship tiles represent vulnerable ships in the human fleet. Civilian ships have no defense; when attacked they are immediately destroyed with no die roll. The tile is turned over to reveal its contents. The fleet loses the indicated resources (usually population) and the ship token is removed from the game. 

When instructed to place a civilian ship and no space area is specified, <span class="cylonfleet">the CAG places it in an area without a civilian ship if possible</span><span class="nocylonfleet">the Admiral may place it in any space area</span>. <span class="newcaprica">During the New Caprica phase, when a ship is evacuated, the current player chooses which space area to place it in. (When evacuating, the only choices are either bottom left or bottom right). </span>

##### Drawing and destroying

When an event instructs players to "draw a civilian ship and destroy it", they are drawn randomly from the stockpile of ships next to the board first, not the main game board. If the only civilian ships left are those on the main board, however, the current player must choose one to destroy. 

<p class="newcaprica" markdown="1">During the New Caprica phase, draw ships to be destroyed from the regular pile first, then the Locked civilian ship stack, then the Prepared civilian ship stack, and finally the main game board. Note that this is *only* for drawing a civilian ship to destroy it, not for any other placement or drawing of civilian ships.</p>


#### Raptors

Raptors aren't used in space areas. Instead, certain game actions will "risk" them (presumably, you are sending them on a dangerous mission to deep space), and a failure will cause them to be destroyed. Just like the other human ships, when destroyed they are removed from the game. If there are no raptors left, players may not use abilities that require risking a raptor. 

<p class="newcaprica">Unlike other combat ships, raptors are usable during the entire New Caprica phase. Players may risk raptors even before Galactica returns to orbit. </p>

#### Vipers

A player who is activating a viper can do one of the following:

<ul>
  <li>Launch: Take the viper from the Reserves and place it in one of the two space areas with a viper launch icon</li>
  <li>Move the viper to an adjacent space area. (There is no flying “above” or “below” Galactica, even though this is outer space.)</li>
  <li><a href="#combat-ship-attack-table">Attack</a> a Cylon ship in the same space area with the viper.</li>
  <li class="cylonfleet">Escort a civilian ship in the viper's space area to safety. The civilian ship is returned to the unused pile, and the viper does not move.</li>
</ul>

When damaged, a viper goes into the "Damaged Viper" box on the board. It may not be launched or used until it is repaired. 

The "Evasive Maneuver" card can only be used when a viper is being "attacked". When any other effect says that a viper is damaged or destroyed, such as "Main Batteries", an "Evasive Maneuver" cannot be used because it is not being attacked.

<p class="daybreak">Under the effects of the "Gravity Well" special rule (on the Event Horizon Crisis), a Skill card must be discarded to activate a viper. For "Command", or any other card that allows a player to activate a viper, 1 card must be discarded for each activation. Actions that allow for moving or attacking with a viper, like "Maximum Firepower" or "Full Throttle", only require 1 card to perform the entire action even if it involves multiple attacks or moves. A pilot moving their own ship during their Movement step or with the first step of an Executive Order is performing a move, not an activation, so a card does not need to be discarded. </p>

<div class="cylonfleet" markdown="1">

#### Mark VII Vipers

The mark VII is a more advanced model of viper. Although they start out damaged, when repaired they are more powerful than regular vipers. Except where noted here, they are treated exactly the same as vipers for all game effects. 

When moving, a mark VII viper may move 2 space areas instead of 1. This includes both manned and unmanned vipers. A mark VII viper is damaged on a 6 or 7, and destroyed on an 8. 

When placing, destroying, activating, or launching a viper, players may choose to use a mark VII viper if one is available instead. 

</div>

<div class="daybreak" markdown="1">

#### Assault raptors

Assault raptors are raptors that have been upgraded for combat instead of reconnaissance, and cannot be used to "risk a raptor". Instead, except where noted here, they are treated exactly the same as vipers for all game effects. 

When the fleet jumps, a character piloting an assault raptor may choose to stay in their space area. An unmanned assault raptor may remain in space if the current player chooses. Assault raptors are destroyed on a 7 or an 8 and are never damaged. If instructed to "damage a viper" and an assault raptor is chosen as the target, it is destroyed instead. 

When placing, destroying, activating, or launching a viper, players may choose to use an assault raptor if one is available instead. 

</div> 

#### Piloting

Any character with piloting in their skill set is allowed to fly a viper themselves. These characters have a piloting token in addition to their regular character token, used to represent which viper they are flying. These are manned vipers, as opposed to unmanned ones. Manned vipers follow the same rules for [activation](#vipers), however a manned viper can only be activated by its pilot. The pilot of a viper that is destroyed is sent to Sickbay. 

When granted a [move](#moves), a pilot may move their ship to an adjacent space area at no cost. By discarding 1 Skill card, a pilot may move to a location on any other ship just like other characters. 

Unless otherwise indicated, whenever a pilot stops piloting their viper is returned to the Reserves. This includes voluntarily moving to a ship location by discarding a card or being involuntarily moved to the Hangar Deck, Sickbay, Brig, Resurrection Ship, etc. 

A pilot may activate their own viper as an action. This could mean moving twice in a single turn, first as a move and again as a viper activation. Note that discarding a card to move to another ship is *not* available as an action or activation, it is strictly a move. 

### Cylon Players

A Cylon player is a player who has revealed as a Cylon<span class="cylonleader"> (or a Cylon Leader who is not Infiltrating)</span>. Players who have a hidden "You Are a Cylon" card are secretly Cylons, but until they reveal they are still treated as human players and not Cylon players. 

Cylon players may choose to ignore:

<ul>
  <li>Abilities on Crisis cards and Skill Checks, unless they specifically say that they apply to Cylon players</li>
  <li class="finalfive">Abilities on Final Five cards</li>
  <li>Being forced to discard Skill cards</li>
</ul>

Cylon players are not allowed to:

- Move to non-Cylon locations (or be sent to the Brig or Sickbay).
- Use abilities or actions on <span class="treachery">non-Treachery</span> Skill cards, Quorum cards, <span class="daybreak">Mutiny cards,</span> or their old human character sheet, including their once-per-game ability. <span class="treachery">They *are* allowed to use the abilities on Treachery cards, unlike human players.</span>
- Be the target of an "Executive Order" or Quorum cards. 
    - If a Cylon reveals as the first action of an Executive Order, they are not allowed to use the second action.
- Contribute more than 1 card to Skill Checks.
- Reveal Loyalty cards that say "immediately reveal this card". 

<p class="exodus">A Cylon is allowed to make use of a "State of Emergency" when it is played, since it targets all players and not a specific player. In addition, "State of Emergency" continues to execute even if the player who played it uses it to reveal as a Cylon.  </p>

#### Cylon Reveal resolution
Revealing as a Cylon is an action, as described on the "You Are A Cylon" Loyalty card. <span class="execution">It can also happen involuntarily, for example as the result of an execution. </span>

<ol>
  <li>Complete the action listed on the “You Are a Cylon” card if this action was taken.
    <ul>
      <li>Note that it says to only reveal <em>this</em> card, not others. Keep any other Loyalty cards secret!</li>
    </ul>
  </li>
  <li>Discard your hand of Skill cards down to 3.</li>
  <li>Discard any Quorum cards <em>attached to your character</em> (these are cards like “Assign Vice President”, not the <em>hand</em> of Quorum cards)<span class="daybreak">, any Mutiny cards, and any Miracle tokens</span>.</li>
  <li>Lose any titles to the character highest in the line of succession for that title. All resources that are tied to that title (the hand of Quorum cards, nuke tokens, Destinations) travel with the title as-is, they are not discarded.</li>
  <li>Move to the “Resurrection Ship” location. If piloting a ship, that ship is returned to the Reserves.</li>
  <li>Unless otherwise directed, draw a Super Crisis Card.</li>
  <li class="daybreak">If you were the Mutineer, hand off the Mutineer card face up to the human player of your choice. They must follow the procedure for receiving the Mutineer card.</li>
  <li class="treachery">If the fleet has traveled 6 or less distance, look at the rest of your Loyalty cards, if any. Hand them all, face down, to <em>one</em> human player of your choice.</li>
  <li>From this point on, follow the rules for a revealed Cylon. This includes <em>not</em> drawing a Crisis card at the end of your turn.</li>
</ol>

#### Cylon game turn

A revealed Cylon player has a modified game turn. 

<ol>
  <li>Draw 2 Skill cards.
    <ul>
      <li><span class="noexpansion">These can be of any two types.</span><span class="expansion">The two cards must be of <em>different</em> types. </span></li>
      <li>As a revealed Cylon, your old human Skill set is ignored entirely.</li>
    </ul>
  </li>
  <li>Movement step:
    <ul>
      <li>Cylon players may move to Cylon locations, but not human ones. <span class="cylonfleet">The “Basestar Bridge” does not require discarding a Skill card from the standard Cylon Locations.</span></li>
      <li class="pegasus">Any <a href="#movement-actions">Movement action</a> that is available to a Cylon, such as those on Treachery skill cards<span class="cylonleader"> or on a Cylon Leader’s character sheet</span>, may be used during this step instead of moving to a new location.</li>
    </ul>
  </li>
  <li>Action:
    <ul>
      <li>Cylons may use the action listed on their current Cylon location, <span class="cylonleader">on their Cylon Leader character sheet, </span><span class="treachery">or on Treachery Skill cards, </span>but <em>not</em> old human character abilities or other Skill cards.</li>
      <li class="expansion">When using the Caprica location, do <em>not</em> ignore the "Prepare for Jump" icon on Crisis cards, even if the board text says otherwise.</li>
    </ul>
  </li>
  <li>End turn.
    <ul>
      <li>Do not draw a Crisis card.</li>
      <li>All players must apply the 10 Skill card hand limit. </li>
      <li>If any resource is at 0 or below, the humans lose.</li>
    </ul>
  </li>
</ol>

<div class="cylonleader" markdown="1">

### Cylon Leaders

A Cylon Leader is a special player who plays with their own motives or agenda. In addition to being aligned with the winning team, a Cylon Leader will also need to accomplish their own goals, adding an extra challenge. These extra goals cannot be discussed or revealed, other than discussing which side the Cylon Leader is trying to help or hurt. 

<p class="pegasus nodaybreak" markdown="1">The Cylon Leader needs to satisfy *all* conditions listed on their Agenda card in order to win. </p>

<p class="daybreak" markdown="1">The Cylon Leader receives 2 Motive cards at the start of the game and 2 more at the Sleeper Agent phase. Each one has 2 components: an allegiance and a condition. The Cylon Leader may reveal a Motive card at any time as long as its condition is *currently* being met, even in the middle of an action or Crisis. If it *was* met before but is not anymore, it cannot be revealed. In order to win with Motive cards, at the end of the game the Cylon Leader must have at least 3 Motive cards revealed, and at least 2 of the revealed cards must be aligned with the winning team. </p>

Cylon Leaders are normally treated like and follow the rules for revealed Cylons. Unlike revealed Cylons, Leaders keep and use their character sheets. Just like other players, they must draw from their Skill set unless otherwise specified. They also have positive abilities at their disposal, including a "once per game" or Miracle ability, and negative abilities which are followed at all times. 

#### Infiltrating

Cylon Leaders are also allowed to Infiltrate by using the "Human Fleet" location. The "Infiltrating" card summarizes the rules for Infiltrating. While Infiltrating, Cylon Leaders follow the rules for *human* players, and are treated as human players in all respects, with the following exceptions: 

- Infiltrators draw 1 extra Skill card (from their Skill set, of course) when they draw cards, for a total of 3.
- Infiltrators cannot hold titles. 
    - They may be assigned Quorum cards like "Mission Specialist" or "Arbitrator", since those are not titles, with the exception of "Vice President" since that requires the ability to become President. 
- Infiltrators may not play more than 2 cards into a Skill check. 
- An Infiltrator has a special action available to them: 
    - Action: Move to the Resurrection Ship. If your character was in the Brig or Detention, discard down to 3 Skill cards before moving. 
- Any time an Infiltrator moves to the Resurrection Ship, either voluntarily using the above action or because of another game effect, they are no longer Infiltrating. They must discard any cards that a revealed Cylon cannot have.

Cylon Leaders always win or lose based on their agenda/motives, regardless of whether they are Infiltrating or not. 

Cylon Leaders always ignore game effects that tell them to add cards to the Loyalty Deck and/or draw from it, since they don't use Loyalty cards. 

</div>

<div class="execution" markdown="1">

### Execution

<ol>
    <li>Discard your entire hand of Skill Cards, as well as any Quorum cards <em>attached to your character</em> (these are cards like “Assign Vice President”, not the <em>hand</em> of Quorum cards)<span class="daybreak">, any Mutiny cards, and any miracle tokens</span>.</li>
    <li class="exodus">If the player executed was the current player, skip their remaining move, action, and Crisis steps.</li>
    <li>Take these steps, depending on whether you are a Cylon or a true human:
      <ul>
        <li>For hidden or revealed Cylons &amp; Cylon Leaders:
          <ul>
            <li>If you are a hidden Cylon player, reveal <em>one</em> “You Are a Cylon” card and keep the rest of your Loyalty cards face down. Do not take the action on that card. <span class="allyseasons">Take 1 Trauma token from the pile (not the Mood Pool).</span> Follow <a href="#cylon-reveal-resolution">the rest of the normal procedure for revealing</a> as a Cylon, but do not draw a Super Crisis card.</li>
            <li>If you are <span class="cylonleader">a Cylon Leader or </span>a Cylon who has already revealed, follow the same instructions as a hidden Cylon but additionally ignore any instructions about Loyalty cards. <span class="cylonleader">As always, Cylon Leaders <em>do not reveal their agenda or motive!</em></span></li>
          </ul>
        </li>
        <li>For a true human player:
          <ol>
            <li>Reveal <em>all</em> your Loyalty cards, showing that none are Cylon cards. <span class="finalfive">For “Final Five” cards, resolve the text for execution.</span></li>
            <li>The fleet loses 1 morale. <span class="pegasus">(If this causes Dee to be executed, resolve her execution after this one is finished.)</span></li>
            <li>Return your character and token(s) to the box, removing them from the game. <span class="allies">Discard any Trauma tokens.</span></li>
            <li class="exodus">Discard all of your loyalty cards.</li>
            <li class="noexodus">If you were Boomer, and the Sleeper Agent phase hasn’t happened yet, draw 1 new Loyalty card.</li>
            <li>Choose a new character, with no restriction on type. You may not choose an alternate version of an existing character, and you may not choose a Cylon Leader. If there are no more characters left, humans lose.</li>
            <li>Start in the normal Setup location for that character, or the Hangar Deck if that location is completely unavailable (for example, Colonial One has been destroyed). <span class="newcaprica">During the New Caprica phase before Galactica returns, start in Resistance HQ.</span></li>
            <li class="exodus">Add 1 card from the “Not a Cylon” deck to the Loyalty deck, shuffle, and draw 1 new card. If you were Boomer, and the Sleeper Agent phase hasn’t happened yet, draw 1 more Loyalty card.</li>
            <li>Distribute any former titles to the first in the line of succession, including the new character. Existing titles do not change hands, even if the new character is higher in the line of succession.</li>
            <li class="daybreak">If the executed character was the Mutineer, the new character receives the Mutineer card again and follows the instructions for becoming the Mutineer.</li>
            <li class="allies">Draw 3 new Trauma tokens. If a “disaster” token is drawn, set it aside and draw a new one to replace it, then return the “disaster” token to the pool (just like at the start of the game).</li>
            <li>If one of these characters was chosen, there are some additional rules and clarifications:
              <ul>
                <li>Boomer:
                  <ul>
                    <li class="noexodus">If before the Sleeper Agent phase, shuffle 1 “Not A Cylon” card into the Loyalty deck.</li>
                    <li>If after the Sleeper Agent phase, start in the Brig<span class="exodus">, shuffle 1 "Not a Cylon" card into the Loyalty deck, and draw an additional Loyalty card</span>.</li>
                  </ul>
                </li>
                <li>Helo: Start out “Stranded” even though it is not the beginning of the game.</li>
                <li>Apollo: Start in a viper from the reserves. If none are available, start in the Hangar Deck.</li>
                <li>Gaius Baltar:
                  <ul>
                    <li>If before the Sleeper Agent phase, shuffle 1 “Not a Cylon” card into the Loyalty deck and draw 1 Loyalty card.</li>
                    <li class="noexodus">If after the Sleeper Agent phase, Gaius may not use his Cylon Detector ability.</li>
                  </ul>
                </li>
                <li>Anders: Skip the Receive Skill cards step of your first turn as Anders due to “Starts on the Bench”.</li>
                <li class="daybreak">Tom Zarek (Military): Draw a Mutiny card due to “Disruptive”.</li>
              </ul>
            </li>
          </ol>
        </li>
      </ul>
    </li>
  </ol>
  
You cannot use a new character's ability on the effect that executed the previous character. For example, if a Quorum card executes a player who comes back as Tory Foster, she cannot then use her "Adaptable" ability on that Quorum card. A new character can, however, use an ability on things that happen soon after. For example, if the Airlock is used to execute a player who comes back as William Adama, the "discard cards" step of the Skill check comes after the "determine result" step which caused the execution. This means that William Adama may use his once-per-game "Command" ability to draw those cards into his hand instead. 

</div>

### Character sheets

Each character's sheet lists these items:

- Type. Used for selecting a well-rounded group of characters to start.
-  Abilities. These include
    - A standard ability, which may be an Action or a passive trait.
    - A "once-per-game" ability, which can only be used once per game (but see ["Once per game"](#once-per-game) below). 
    - A negative ability which must be followed during the game.
- Skill Set. These are the types of cards that your character draws. When instructed to draw Skill Cards, *they must come from your skill set* unless otherwise instructed. 
- Setup instructions. This tells you how to start the game with your character. 

A character ability that affects Crisis cards or Skill checks does *not* affect a Super Crisis card. 

#### Notes

When Helo re-rolls a die that had "Strategic Planning" applied to it, the re-roll also receives +2 to the result. Players may also choose to apply "Strategic Planning" to the re-roll if it wasn't used originally. 

Helo's "Moral Compass" allows him to choose which box is resolved on a "[Someone] Chooses" Crisis. He cannot, however, influence any choices that must be made *within* that box. For example, if the chosen box says that the President must either give up their title or go to the Brig, Helo does not have the power to influence the President's decision. 

<div class="pegasus" markdown="1">

If Helena Cain uses "Blind Jump" while a "Mission Specialist" is assigned, ignore the "Mission Specialist" instructions for that jump, leaving it in place for later. Draw 2 civilian ships and destroy them [according to the normal rules for "drawing and destroying"](#drawing-and-destroying). If a "Mission Specialist" has been assigned, ignore that card for this jump.

When "Kat" uses her "Hot Shot" ability, there is no die roll, so cards that affect die rolls cannot be used. An attack on a basestar with structural damage does not get +2 with "Hot Shot" for the same reason. 

If Ellen Tigh steals a title using "Manipulative" but then loses it before the end of her turn, she is no longer capable of following the instruction to return it and it therefore stays with whoever now has it. If she attempts to return it but the player it belonged to is no longer capable of holding the title, it goes to the first in the appropriate line of succession. If she became President again during her "Manipulative" turn through some other action, she must still follow the instruction to return the title at the end of her turn. She cannot use "Manipulative" to become Admiral if she is in the Brig. 

</div>

<p class="exodus" markdown="1">
Hoshi's<span class="pegasus"> "Reluctant" weakness also applies to Movement actions on Skill cards (as does any game effect that affects actions of a particular type). His</span> "Dutiful" ability can be used once for *each* location listed. So, for example, if he used his "Organized" once-per-game on those 3 locations, he could also use "Dutiful" each time on them.
</p>

<div class="daybreak" markdown="1">

Tom Zarek (Military)'s "Necessary Steps" applies to all players, even himself, and even applies at the start of the game when he draws a Mutiny card due to his "Disreputable" weakness. 

Lee Adama (Political)'s "Choose a Different Path" and Helo's "Moral Compass" both apply to "[Someone] Chooses" decisions. Lee's ability creates a new, additional option on "[Someone] Chooses" cards, whereas Helo's forces a player to choose a specific box to resolve. Lee could use "Different Path" to create a new option (and presumably choose it), and Helo could force his choice to something else. But if Helo uses "Moral Compass", Lee cannot then use "Different Path" to change away from Helo's choice, because "Different Path" would simply add a new option, not force Helo to actually use it. 

Doc Cottle can use *abilities* on Engineering cards, just not Actions. As always, this restriction also applies to Movement actions.

</div>

<div class="cylonleader" markdown="1">

Athena's "For Love" ability cannot be used in the following cases:

- A player is discarding down to their hand limit (as mentioned on the sheet)
- A Super Crisis card is being resolved
- A player is discarding to resolve a character ability, like Kat's "Hotshot", Hoshi's "Dutiful", and Anders's "Star Player". 

D'Anna's "Visions" ability, when used in the Brig or Detention, does not require discarding down to 3 cards since it is not using the Infiltrator's special "return to the Resurrection Ship" Action. 

Doral's "Meticulous" ability does not allow him to choose the Resurrection Ship as his destination. This is because the Resurrection Ship is Hazardous, and the instruction to "move to any Cylon Location" does not explicitly include the Resurrection Ship as a potential destination.  

</div>

### Once per game

<div class="nodaybreak" markdown="1">
The "once per game" ability on a character sheet can be chosen by a player up to once in a single game. This is tied to a player, not to a character, so if a player uses their once-per-game and is later executed, they may not use the new character's once-per-game ability at all. Conversely, an executed player who has not used their once-per-game may use their new character's ability later.

Even when not playing with Daybreak, it's convenient to use that expansion's Miracle tokens to track whether each player has used their once-per-game or not. 
</div>

<div class="daybreak" markdown="1">
The Daybreak expansion changes the rules for character abilities specified as "once per game". They are now also known as Miracle abilities, and they may be used more than once per game. 

To use a Miracle ability, a player must first discard 1 Miracle token. Any player with a Miracle token may use their Miracle ability, even if they have used it before. Players who do not have a Miracle token may not use their Miracle ability. 

There is a limit of 1 Miracle token per player, and they cannot be exchanged or traded between players. Players who already have one cannot gain another. The Support version of Gaius Baltar is an exception to all of these rules: he may hold up to 3 Miracle tokens and has an action that allows him to give and take Miracle tokens from other players. 

</div>


### Loyalty cards

Players receive Loyalty cards at the beginning of the game and at the Sleeper Agent phase. Players must keep them secret unless another player is directed to look at them, not discussing their text or any other details other than claiming to be human or Cylon. Players may *imply* things about their Loyalty cards (for example, "I wouldn't examine my Loyalty cards if I were you" or "This is a waste of time") but may not explicitly say that they have certain types of cards. 

When a player is allowed to examine just one from another player, it is chosen randomly. When choosing a target to inspect Loyalty cards, players must select a face down Loyalty card that doesn't belong to themselves if possible. If this is not possible, for example a player who is President and Admiral being instructed to inspect the President or Admiral's Loyalty card, nothing happens. 

A player is on the Cylon team if *any* of their Loyalty card states "You Are A Cylon". Having both a "Cylon" and "Not A Cylon" means that you are on the Cylon team, even if you're still hidden. A hidden Cylon still wins or loses with the Cylons. 

When a game element refers to "players", it is talking about all individuals currently playing the game. "Human players" are players who are not revealed Cylons. Even if a player is concealing a "You Are a Cylon" card, they are still treated as a human player in all respects until they reveal. "Cylon players" refers only to revealed Cylons. <span class="cylonleader">Cylon Leaders straddle this line, being considered "Cylon players" when not Infiltrating, and "human players" in all respects (with some limitations) when Infiltrating.</span>

<div class="personalgoal" markdown="1">

#### Personal Goal cards

A Personal Goal card counts as a "Not a Cylon" card, but adds a secret goal for that character to accomplish which probably goes against the humans' goals. They require an _extra-card_ style Loyalty deck, where 1 card is always left over. 

Personal Goals can be revealed as an action as long as certain conditions are met. After revealing, if the distance is 6 or less, the player must then shuffle 1 card from the "Not a Cylon" deck into the Loyalty deck and draw 1 new Loyalty card. 

If a human player has not revealed it by the end of the game, the resource listed on the card is reduced which could cause the humans to lose at the last minute. If a revealed Cylon player has an unrevealed Personal Goal, resources are *not* lost.

</div>

<div class="finalfive" markdown="1">

#### Final Five cards

A Final Five card counts as a "Not a Cylon" card, but can trigger special negative effects when revealed or examined. (Thematically, the Final Five are supposed to be Cylons, but they are not like the other seven and so they count as human for game purposes.)

When a player looks at another's Loyalty card and it turns out to be a Final Five card, they must immediately reveal it and return it to the other player, who resolves the text on the card, turns it face down again, and shuffles it with their other Loyalty cards if any. 

If, during an execution, a Final Five card is revealed, the text on the card is resolved by the executed player and the Final Five card is removed from the game. 

The current player may choose the order in which to resolve Final Five cards, but they must *all* be resolved, even if one of them causes the player who examined the Final Five cards to be executed midway through. 

Abilities on Final Five cards do not affect Cylon players. 

</div>

<div class="nodaybreak nocylonleader" markdown="1">

#### The Sympathizer
This card is sometimes added to the Loyalty deck after the first round of cards go out (so it will probably, but not always, appear in the Sleeper Agent phase). As it states on the card, this Loyalty card is immediately revealed and resolved when dealt to a human player. If any resource is in the red zone, The Sympathizer is sent to the Brig but remains a human player. If there are no resources in the red zone, The Sympathizer follows [the procedure for revealing as a Cylon player](#cylon-reveal-resolution), and plays as a (restricted) Cylon the rest of the game. 

This card is somewhat controversial — it attempts to be a sort of "half-Cylon", but The Sympathizer is limited in what they can do as a Cylon, and they don't get to sneak around pretending to be human at all, since they're revealed right away. There is an official "No Sympathizer" variant, handicapping the humans to making the Sympathizer unnecessary, which is described in the Loyalty deck section. Many of the expansions contain ways to replace The Sympathizer with something else as well (Cylon Leaders, The Mutineer, Sympathetic Cylon). 

</div>

<div class="pegasus nodaybreak nocylonleader" markdown="1">

#### Sympathetic Cylon

This card is similar to The Sympathizer, as described above. It is included in the Loyalty deck after the first round of cards go out, so it will probably appear in the Sleeper Agent phase. However, the Sympathetic Cylon is more like a simplified Cylon Leader. 

As stated on the card, the Sympathetic Cylon card is immediately revealed when dealt to a human player. The player [reveals as a Cylon](#cylon-reveal-resolution), but also draws a Sympathetic Agenda card (the ones with 4-6 and Caprica Six on the back). The Sympathetic Cylon must meet all the conditions on the card to win at the end of the game. They are also allowed to Infiltrate, following the rules listed on the "Infiltration" card. <span class="cylonfleet">Sympathetic Cylons are not allowed to become CAG, President, or Admiral.</span> 

Whenever the Sympathetic Cylon moves to the Resurrection Ship, either voluntarily with the action on the "Infiltration" card or through any other game effect, they are no longer Infiltrating. They must discard any cards that a revealed Cylon is not allowed to have. 

The Sympathetic Cylon does not use their old human character sheet. It is disregarded just like a standard Cylon player. While Infiltrating, since they no longer have a Skill set, they draw in the normal fashion for a Cylon, drawing their choice of Skill cards of different types, but with a 1 card bonus, for a total of 3 Skill cards of different types. 

</div>

<div class="daybreak" markdown="1">

#### The Mutineer

The Mutineer card replaces the Sympathizer role. It is always treated as a "Not a Cylon" card. The player with this card is referred to as "The Mutineer". 

Whenever a human player receives the Mutineer card, they must reveal the card. If it was received from the Loyalty deck, not another player, they must then draw another Loyalty card to replace it. Then, they must draw 1 Mutiny card and are stripped of any Titles that they hold, giving them to the character highest in the line of succession not including themselves. (This only happens when receiving the card. The Mutineer is allowed to gain or lose titles after that as normal.)

When resolving the Crisis card drawn at the end of their turn, if the Mutineer resolves a "Prepare for Jump" icon, they must draw 1 Mutiny card.

The Mutineer is allowed to hold 2 Mutiny cards without being sent to the Brig, and may hold 2 Mutiny cards in the Brig instead of 1. 

### Mutiny cards

Mutiny cards provide actions to players, usually with both some benefit and some drawback. They are kept secret from other players, and discarded face up next to the Mutiny deck. 

Mutiny cards cannot be drawn, held, or played by revealed Cylons, and revealed Cylons cannot be selected as the target of an effect that says to draw Mutiny cards. 

All human players have a limit of 1 Mutiny card except for the Mutineer, who has a limit of 2. 

#### Drawing a second Mutiny card

When a player draws a Mutiny card above their limit, they are immediately sent to the Brig unless specifically told not to do so (or incapable of moving to the Brig, such as when "Helo" is Stranded). 

Once the move to the Brig is resolved or skipped, the player must immediately choose Mutiny cards to discard until they are at their limit again.

#### Discarding Treachery cards and gaining Mutiny cards

Some Treachery cards say to draw a Mutiny card if they are chosen to be discarded. Players are considered to have "chosen" any time they discard unless either

- a game effect directed them to choose randomly, or
- they were specifically instructed to discard "all" of their Skill cards, no number was given.

Note that if a player is instructed to discard a specific number of cards, it is considered a choice even if they will have to discard all of their cards to reach that number.

Within one turn, a player does not draw more than 1 Mutiny card as a result of discarding Treachery. After the first Mutiny card is received, that text is ignored on subsequent cards discarded by that player for the rest of the turn. 

Cards being discarded after being added to a Skill check do not count as being chosen by anyone. Playing a card into a Skill check does not count as discarding it.

</div>

<div class="allies" markdown="1">

<div class="ioniannebula" markdown="1">

### Trauma Tokens

Trauma Tokens are used for the Ionian Nebula ending. Each one represents a hardship that the character is encountering and how the character reacted to it. The "benevolent" symbol represents compassion or belief in destiny. This can be beneficial to humans, but may alienate a Cylon from the other models. The "antagonistic" symbol represents bitterness or aggression, which can be harmful to human players. The "disaster" symbol represents a tragedy. 

![The icons on Trauma Tokens](images/trauma_tokens.png)

When drawing Trauma Tokens:

- Look at the tokens. If they are not "disaster" tokens, keep them next to your character sheet face down with any other tokens and do not reveal them to anyone.
- If one is a "disaster" token, reveal it.
   - If you are a human player, your character is executed, and the "disaster" token is returned to the pool. 
   - A revealed Cylon draws 2 more Trauma Tokens, then puts any "disaster" tokens back into the pool. (Note that those 2 new tokens could contain another "disaster", requiring 2 *more* tokens, etc.)

When instructed to draw multiple Trauma tokens, draw all of them at once, not one at a time, before resolving any "disaster" tokens.

#### Trauma tokens on locations

If a player starts their turn on a location with a Trauma Token, such as the Brig or Sickbay, they draw that token at the start of their turn and replace it with a new token from the pool.

</div>

<div class="allyseasons" markdown="1">
### Trauma Tokens

Players must draw a Trauma token when they are sent to the Brig or Sickbay. In addition, a hidden Cylon who is executed must draw a Trauma token upon entering the Resurrection Ship. 

### Mood Pool

The Mood Pool represents the overall mood of the ship. Humans want the mood to be Benevolent, but must also keep their own stock of tokens Benevolent, and the reverse is true for Cylons. When encountering allies, they will be more likely to be helpful or hurtful based on the mood of the ship. 

The Mood Pool must always have the same number of tokens as there are players, except in the middle of an Ally encounter. When instructed to draw or discard tokens in any other way, players always use the regular pool, not the Mood Pool.

### Trauma phase

At distance 8, all players reveal their Trauma tokens. Human players count Benevolent tokens as positive and Antagonistic tokens as negative. Cylon players count Antagonistic as positive and Benevolent as negative. Each is weighted equally at either +1 or -1. 

Players total their net score and each does the following:

|  Net score  | Result                               |
|-------------|--------------------------------------|
| -3 or lower | The player is executed.              |
|     -2      | Discard 4 Skill cards.               |
|     -1      | Discard 2 Skill cards.               |
|      0      | Nothing happens.                     |
|     +1      | Draw 2 Skill cards of any type.      |
|     +2      | Draw 4 Skill cards of any type.      |
| +3 or more  | Add or subtract 1 from any resource. |


</div>

### Ally cards

Ally cards represent other characters in the fleet who can help or hurt. They appear in various locations and have an associated Trauma token which indicates what they do when encountered. When a player ends their Movement step in a location with an Ally, they *must* encounter it. 

To encounter an Ally: 

<div class="ioniannebula" markdown="1">

1. Reveal the Trauma Token on that Ally's card. Follow the action on the card that is indicated by the token. If it is a "disaster" icon, do not resolve either action. The player encountering the Ally is not affected.
1. Remove the Ally card and token from the game. 
1. Discard the revealed Trauma token. 
1. Draw a new Ally card, and place its corresponding token on the indicated location.
1. The player who encountered the Ally chooses one of *their own* Trauma tokens and places it on the new Ally card without revealing it. If they have no Trauma tokens, use one from the pool without examining it. 

</div>

<div class="allyseasons" markdown="1">

1. Draw a token from the Mood Pool and reveal it. Follow the action on the Ally card indicated by the token.
1. Remove the Ally card from the game. 
1. The player who encountered the Ally takes the Trauma token that was revealed, adds it to their own stock, and randomizes their tokens.
1. Draw a new Ally card, and place its corresponding token on the indicated location.
1. The player who encountered the Ally chooses one of *their own* Trauma tokens and secretly adds it to the Mood Pool, which is then shuffled. 

</div>


#### Placing a new Ally

Allies are always removed from the game if a player has used or is using the corresponding character for themselves. This includes characters that were played but have since been executed. When this occurs, keep drawing Allies to replace the removed Ally until one is found that has not been used. Remember that "Boomer" and "Athena" are *not* duplicates of each other, so they may coexist within the same game. 

Allies are not reused, so if the deck is depleted no more Allies are placed. 

#### Ally replaced with player, or location damaged

If a player chooses a character after an execution that corresponds with an Ally currently on the board, or if a location containing an Ally is damaged or destroyed, the Ally is removed from the game <span class="ioniannebula">(without revealing the Trauma token)</span> and a new non-duplicate Ally is drawn. 

<p class="ioniannebula" markdown="1">The Cylon player with the most Trauma tokens chooses one of *their own* tokens to place on the new Ally. If there is a tie for most tokens, the current player chooses which Cylon player places the token. If there are no Cylon players with tokens, draw a random token from the pool to place on the Ally. </p>

<p class="allyseasons" markdown="1">After this occurs, the Cylon player with the most Trauma tokens may look at the entire Mood Pool and exchange one token from the Mood Pool with one of their own. If there is a tie for most tokens, the current player selects which tied player examines the pool. If there are no Cylon players with tokens, nothing happens.</p>

</div>

### Titles

Titles can only be held by human players. <span class="cylonleader">Cylon Leaders cannot hold titles even while Infiltrating.</span> When a human is revealed to be a Cylon, the first player in the line of succession for that title claims it. 

The lines of succession are *not* applied automatically. Players do not gain titles solely because they are highest in the line of succession. When a player holds a title, they keep it until they are instructed to give it to someone else. 

#### President
The President controls the hand of Quorum cards on behalf of the fleet and has to make choices on Crisis Cards. 

The hand of Quorum cards belongs to the human fleet as a whole, but is controlled and kept secret by the President. If the President title is transferred to another player for any reason, the new President also takes the hand of Quorum cards. They are not discarded or kept by the outgoing President. The President title card provides an additional action to draw Quorum cards, and some locations can only be used by the President. <span class="expansion">There is a 10 card limit to the hand of Quorum cards.</span><span class="noexpansion">There is no card limit for the hand of Quorum cards.</span>

<p class="pegasus">The "Consult the Oracle" card cannot be used on the Loyalty deck<span class="cylonleader pegasus"> or the Agenda deck</span><span class="cylonleader daybreak"> or the Motive deck</span>.</p>

#### Admiral

The Admiral controls the human fleet's nuke tokens and can use them with the Action on the title card. The Admiral also chooses the destination when a jump occurs, and makes choices that specify the Admiral. <span class="nocylonfleet">In addition, if a game effect states that a civilian ship is to be placed, but does not specify a specific area, the Admiral may choose any area.</span>

If the Admiral is in the Brig, they are stripped of their title. The highest human player in the line of succession then claims it. The Admiral does not regain their title automatically upon leaving the Brig. If all eligible players are in the Brig, the title goes to the highest in the line of succession until a player leaves the Brig. <span class="newcaprica">The Admiral still retains their title if they are sent to Detention on New Caprica.</span>

<div class="cylonfleet" markdown="1">

#### CAG (Commander, Air Group)

The CAG makes some Crisis decisions, and is in charge of placing civilian ships on the game board when no space area is specified. This typically occurs as a result of the Pursuit track advancing. 

When a game effects says to place a civilian ship on the board, but does not specify a specific space area, the CAG places the ship on the board. The CAG must choose a space area *without* a civilian ship already in it, if possible. 

The CAG follows the same rules as the Admiral when sent to the Brig<span class="newcaprica"> (but not Detention)</span>: they are stripped of their title and do not automatically regain it upon leaving the Brig. 
</div>


### Locations

Some ships are much larger than combat ships, and they contain locations that a player may move to to perform a location-based action. Cylon locations are accessible to revealed Cylon players only, and human locations are accessible to human players only. 

During a move, a player may move to any location on the same ship at no cost. There is no die rolling to move a number of "spaces", the character's token is simply moved to the desired location. When moving between ships, a player must first discard 1 Skill card. A player piloting a combat ship may also do this, see the ["Piloting"](#piloting) section for more details. 

Players may only move to hazardous locations via game effects that specifically refer to that location. Hazardous locations are marked by a "caution" border.

#### Commmand

The wording on Command should actually be "Perform up to 2 unmanned viper activations", because you are allowed to target the same viper twice. This also means that you may launch a viper for the first activation and then activate it again for the second. 

#### The Brig

Players may only move out of the Brig if they pass the Brig's Skill Check, or an effect says to move a player and *specifically* mentions the Brig. For example, a card that says "send the President to Sickbay" cannot be used to transfer the President from the Brig. If, however, it said "move a character from the Brig to Sickbay", such a move would be allowed. In particular, Kat's "Stim Junkie" and Lee Adama's "Alert Viper Pilot" cannot be used to escape the Brig. 

The text on the board says that characters can move to "any location" after passing the Skill check, but the base game rules specify on page 18 that they can move to any location *on Galactica*. 

Players may use any action they wish in the Brig. <span class="pegasus">They are also allowed to use Movement actions during their Movement phase.</span> The only restrictions that apply to them are the move restriction and the number of cards they may play into Skill checks. 

#### Choosing players for Sickbay/Brig

In general, players may make choices even if they know that they cannot fulfill the consequences of that choice. However, when choosing a character to send to the Brig or Sickbay, the end result must be a character moving from somewhere else to that location *if possible*. Characters with restricted movement (like being in the Brig, Helo's "Stranded", or a revealed Cylon) and characters that are already in that location are *not* valid targets to be sent to Sickbay or the Brig. 

If no player exists who can be sent to the Brig or Sickbay, the overall effect can still be chosen, and the result is that nothing happens. If a subsequent effect depended on which character was chosen (for example, "Choose a character to send to Sickbay, then look at one of their Loyalty cards"), the subsequent effect is also skipped because no player could be chosen. 

If such an effect targets a specific player, like the President, and that player cannot be moved or is already in that location, the effect can still be chosen, and the result is that nothing happens. For example, if given a choice between "Lose 2 morale" and "send the President to Sickbay", but the President is in the Brig, the second option can be chosen to avoid losing morale. 

#### Colonial One

If Colonial One is destroyed by a game effect, flip the overlay over to reveal the "Colonial One Destroyed" side. (These effects are only included in Daybreak which includes the overlay.) Any characters on board are sent to Sickbay, and the locations on Colonial One are inaccessible for the rest of the game.

During the New Caprica phase, Colonial One is not accessible. (It is being held captive by the Cylons on New Caprica, and won't escape until after the game ends.)

<div class="searchforhome" markdown="1">

#### Demetrius & Missions

Demetrius is used in the "Search for Home" ending. Its locations cannot be damaged. 

To activate a Mission, place the top card of the Mission deck face up on the "Active Mission" space. It is immediately resolved. Missions have the following restrictions:

- The Skill check on a Mission card cannot be modified by character abilities or card abilities. This includes abilities that happen "before" or "after" Skill checks. 
- Skill Check Abilities are *not* resolved during a Mission.
- Mission cards are not Crisis cards, so abilities that apply to Crisis cards do not apply to Mission cards. 

Otherwise, Missions are resolved [in the same way as other Skill checks](#skill-check-resolution). Limits on how many cards a player may contribute *do* still apply when attempting a Mission. 

Mission cards *always* remain on the Active Mission space until the fleet jumps. Unless otherwise instructed, leave the Active Mission face up after it is finished. Since new Missions cannot be activated while a card remains, this means that only one Mission can be attempted per jump. 

Any distance gained from a Mission card applies _immediately_, even though the card is not supposed to be put next to the other Destination cards until the fleet jumps. This could trigger the Sleeper Agent phase or anything else that occurs at a specific distance. The only reason it stays in the Active Mission space is to remind players that they cannot attempt another Mission until the next jump.

Follow the instructions on the Active Mission space when jumping to clear the Active Mission. (Essentially, Missions without distance are always discarded, Missions with distance are shuffled back into the deck if failed or are put next to the other distance cards when passed.)

#### Rebel Basestar (human or Cylon)

The Rebel Basestar is only accessible after it is added to the game by the "Cylon Civil War" Mission card. Depending on the result of this Mission, it is either a Cylon location or a human location. Players of the correct allegiance can travel to it by discarding a Skill card. As always, a Cylon Leader is considered a Cylon unless they are Infiltrating, in which case they are a human player.  

For Raider Bay, the player is only allowed to activate the ships being placed, not any ships that were already present. Apollo may use "Alert Viper Pilot" to commandeer a viper placed using Raider Bay. In this case, Apollo can use the Action granted by "Alert Viper Pilot" in the middle of the Raider Bay Action, and the player using Raider Bay cannot activate Apollo's viper since it is being piloted. 

</div>


<div class="exodus nopegasus nodaybreak" markdown="1">
#### The "Caprica" location

In the Exodus rulebook on page 9, the Caprica location's behavior is changed: do *not* skip the "Prepare for Jump" icon on Crisis cards played using Caprica. 

</div>

#### Cylon Fleet location

When choosing to "activate all Cylon ships of one type", the chosen activation is [resolved exactly as it is when it appears on a Crisis card](#activating-cylon-ships), including moving Centurions if heavy raiders are activated <span class="cylonfleet">and placing ships on the Cylon Fleet board</span>.

The other option, "launch 2 raiders and 1 heavy raider from each basestar" does exactly that and nothing more. <span class="cylonfleet">The Cylon Fleet board is only involved if the regular piles have run out of raiders or heavy raiders, in which case they are drawn from the lowest numbered Cylon Fleet spaces as usual. Basestars on the Cylon Fleet board do *not* launch ships as a result of this action, only basestars on the main board. Since it is not an "Activate Cylon Ships" icon that is being resolved, the Pursuit track is not advanced and Cylon Fleet space areas are not moved over to the main board. </span>

<div class="cylonfleet" markdown="1">
#### Basestar Bridge

The Basestar Bridge on the Cylon Fleet board is a Cylon location, only accessible to Cylon players. It is *not* necessary to discard a Skill card to move to the Basestar Bridge from the regular Cylon Locations. 

The two abilities that are chosen must be decided *before* resolving either of them. The activating player may not switch after the first has been resolved. (This is an exception to the normal rules. For most other game decisions, players are allowed to make their choices as they go.)

Attempting to use the Basestar Bridge to place a basestar on the Cylon Fleet board when both basestars are already placed on one of the two boards does nothing. Both basestars remain wherever they are. 

</div>

## Ending the game

### Final jump

<p class="nonewcaprica" markdown="1">
After the final distance indicated on the Objective card is reached, the next time the fleet jumps will end the game. When this happens the [normal jump procedure](#jumping-the-fleet) is skipped, so there is no "Remove Ships" step and no Destination card is drawn. The "FTL Control" location does still trigger a population loss if the die roll is 6 or lower, since this happens before the the jump. 
</p>

<p class="newcaprica" markdown="1">
At the distance indicated on the New Caprica objective card, the New Caprica phase begins. After setup is complete, the game continues. The jump track is used one last time to bring back Galactica, and after Galactica returns the final jump will end the game. This jump can only be initiated by the Admiral as an action, not by using the jump track or "FTL control". The [normal jump procedure](#jumping-the-fleet) is skipped, so there is no "Remove Ships" step and no Destination card is drawn. Instead, follow [the rules listed below for ending the game in New Caprica](#evacuating-new-caprica). 
</p>

<p class="cylonleader">After the final jump, Cylon Leaders must wait until all other effects are resolved before they can <span class="daybreak">reveal any "game is over" Motive cards</span><span class="pegasus nodaybreak">declare their Agenda fulfilled</span>.</p>

<p class="pegasus">"Preventative Policy" cards remain in effect if they were played during the last turn.</p> 

When resolving any end of game effects, unrevealed Cylons continue to be treated as human players but they still ultimately win or lose with the Cylons. <span class="cylonleader">Cylon Leaders continue to be treated as human or Cylon players depending on whether or not they are Infiltrating.</span><span class="personalgoal">Resolve game effects that take place at the end of the game such as unfulfilled Personal Goal cards. </span>If the humans have not run out of resources or lost by any other means when everything is resolved at the end of the game, the humans win!

<div class="newcaprica" markdown="1">

### New Caprica phase

The humans reach New Caprica and attempt to settle there. However, they are discovered by the Cylons and both Galactica and Pegasus are forced to flee. The humans will have to wait out the Cylon occupation while waiting for their battlestars to return. Once they do, the humans will have to evacuate New Caprica and jump away to safety. 

#### New Caprica phase setup

1. If this distance was reached via a jump, go through the [normal jump procedure](#jumping-the-fleet) including resetting the fleet marker. <span class="daybreak">Assault raptors are not allowed to remain in space for this jump.</span>
    - If the destination is "Gas Cloud", the Admiral examines the New Caprica Crisis deck after it is ready, not the old Crisis deck.
    - The Destination card could put ships back on the main game board after the jump. Leave them where they are unless instructed otherwise. 
2. If it was not reached via a jump (for example earning distance through a Skill check), just reset the fleet marker. 
4. Remove the Crisis deck from the game. The New Caprica Crisis deck will be used in its place for the rest of the game. Shuffle it and place it next to the board. 
5. The New Caprica game board is placed above the Pegasus game board.
6. All human players are moved to "Resistance HQ", including any in the Brig. Piloted vipers are returned to the Reserves. 
7. All Cylon players are moved to "Occupation Authority".
8. Move all remaining raptors to the New Caprica board. They can still be risked even before Galactica returns. 
9. Shuffle all remaining civilian ships, whether in space or in the stockpile, into a single face down stack on the "Locked Civilian Ships" box. 

Once setup is complete, the game continues from where it left off, now with the rules for the New Caprica phase "before Galactica returns" in effect. 

Until Galactica returns to orbit, as described below, all players may only move to New Caprica locations. Even when it does, Colonial One will not return. (Story wise, it was the last ship to leave New Caprica, and it was under Cylon control until then.) You may choose to use the Colonial One overlay's "destroyed" side to indicate this. 

#### New Caprica phase rules

The New Caprica Crisis deck replaces the regular Crisis deck for the rest of the game. Game effects that target the Crisis deck now target that deck. The "Prepare for Jump" icon is always accompanied by an "evacuation icon" when it appears on New Caprica Crisis cards. "Prepare for Jump" is resolved before Galactica returns, and the evacuation icon is resolved afterward. 

Locations on New Caprica can have "Human" and "Cylon" actions. Only human players may use human actions, and only Cylon players may use Cylon actions, but both types of player are allowed to move to New Caprica locations. There are also 2 actions at the top of the board that can be used while on any New Caprica space. (For Laura Roslin's "Terminal Illness", these 2 actions do not count as activating a location.) Athena's "Resolute" ability can activate either the human or Cylon action on a New Caprica location.

As stated on the alternate President card, when the President plays a Quorum card, they must roll a die, and if 3 or lower move to Detention. This only occurs when playing a Quorum card *from the hand*, not, for example, using Roslin's "Skilled Politician" once-per-game.

##### Occupation Forces

Occupation Forces are a new type of Cylon "ship", which travel along the track at the bottom of the New Caprica board. When they are activated:

- An Occupation Force that is already at the Shipyard location leaves the board. As it leaves, it destroys a Locked civilian ship if any exist, otherwise it destroys a Prepared civilian ship, and does nothing if both stacks are empty. 
- Occupation Forces not at the Shipyard move one space to the right along the track.
- If there were no Occupation Forces on the track when they were activated, place one at the start under Occupation Authority. 

Centurions do not advance when Occupation Forces are activated and vice versa, they are completely separate from one another although they look similar. 

When re-rolling an "Attack Occupation Force" roll using "Maximum Firepower" that also had "Maximum Firepower" applied to it, the re-roll also gets the "Maximum Firepower" bonus. 

##### Preparing civilian ships

To "prepare" a civilian ship, move the civilian ship on the *top* of the Locked Civilian Ships stack to the *bottom* of the Prepared Civilian Ships stack. 

Civilian ships may *only* be moved out of the Locked or Prepared Civilian Ship stacks via game effects that *specifically* refer to those places. When another game effect requires drawing or placing civilian ships, they must be drawn from the regular pile by the board as usual. Unless ships have been escorted off the board, this probably means that there won't be any, and [normal component limitation rules](#component-limitations) will apply. 

The rules for resolving an instruction to ["draw and destroy a civilian ship"](#drawing-and-destroying) have special instructions for the Locked and Prepared stacks. 

##### Brig versus Detention

When a character is on a New Caprica location:

- Any effect that sends them to the Brig instead sends them to Detention
- Any character ability that applies to the Brig instead applies to Detention
- If the President is on New Caprica, Quorum cards that apply to the Brig instead apply to Detention

Just like the Brig, a Cylon who reveals in Detention does not get to use the ability on their Loyalty card. Similar to the Brig, when the board says that a player who passes the Skill check may move to "any location", it actually means any location _on New Caprica_.

Unlike the Brig, the Admiral does not lose their title when sent to Detention. Players still draw a Crisis card in Detention. 

If Romo Lampkin is on New Caprica and uses "Attorney", he instead moves a character from Detention to a location on New Caprica and steals their Skill cards. 

##### Before Galactica returns

The Galactica space areas, Cylon Fleet board, and any Cylon ships or Centurions in them are not affected by any game mechanics. Ignore any Cylon ship activations on Crisis cards, with the exception of "Occupation Forces" activations, and ignore anything that says to place, move, destroy, repair, or damage any ships including vipers, Galactica, and Pegasus. Civilian ships cannot be placed in space areas. 

Raptors **may still be risked** before Galactica returns.

Resolve New Caprica Crisis cards as normal, ignoring the "evacuation icon" for now. The fleet marker advances as usual. When it reaches "Auto Jump", Galactica returns and the final battle is set up. 

Game effects that would normally send a player to the Resurrection Ship instead send them to the Medical Center. This includes <span class="cylonleader">ending Infiltration, </span> execution, and revealing as a Cylon. <span class="cylonleader">Cylon Leaders cannot begin infiltrating because they would need to move to the Human Fleet location first. </span>

If Felix Gaeta uses his "Coup" Action while in Detention before Galactica returns, he moves to Resistance HQ instead of Command. Doral's "Meticulous" ability instead allows him to move to any New Caprica location and take another action instead of moving to the Medical Center. 

Only locations on New Caprica may be activated. Athena's "Resolute" ability and Hoshi's "Organized" ability cannot access any unavailable locations. The "Evacuation" icon on Crisis cards does not have any effect until Galactica returns, but the "Prepare for Jump" icon is still used to move the Jump Preparation track. 

##### After Galactica returns

![Ship layout for when Galactica returns, described below](images/final_battle.png)

When Galactica returns, first place 1 basestar and 4 raiders in each of the 2 top space areas. Place 2 vipers in each of the 2 bottom space areas (4 total). 

The jump track will not be used for the rest of the game, so from now on ignore the "Prepare to Jump" icon on Crisis cards. Instead, resolve the "Evacuation" icon as follows:

- The current player takes a civilian ship from the top of the Prepared Civilian Ships space and moves it one of the two space areas with a viper launch icon. 
- If no ships are in the Prepared pile, nothing happens. Since the final jump is at the sole discretion of the Admiral, do not use the jump track. 

Human players may now move between New Caprica, Galactica, and the other human ships by discarding a Skill card. Colonial One is not accessible during the New Caprica phase even after Galactica comes back. Cylon players may also move between New Caprica and the other Cylon locations by discarding a Skill card. Ships and Centurions are once again affected by game effects as normal. 

<p class="cylonfleet">Vipers may use their "escort civilian ships" activation on civilian ships in their space area.</p>

##### Evacuating New Caprica

Once Galactica is in orbit, the Admiral has an action available on their title card which initiates the final jump and ends the game. 

When the final jump occurs:

- Destroy all civilian ships still on New Caprica. 
- Execute all human players on New Caprica. 
- After these steps are fully resolved, [resolve all other "end of game" effects as normal](#final-jump). 

</div>

<div class="ioniannebula" markdown="1">

### Ionian Nebula: Crossroads Phase

The Crossroads phase begins immediately upon reaching 8 or more distance, interrupting the current player's turn. The Cylons are lying in wait to ambush the human fleet, and each character faces an important decision. There are 3 steps to the Crossroads phase:

![Ship layout for Crossroads, described below](images/final_battle.png)

1. Set up the Battle of the Ionian Nebula:
    1. Place a basestar and 4 raiders in each of the top 2 main board space areas. 
    2. Launch 2 vipers into each of the two bottom space areas. 
    3. Finally, reset the jump track.

2. Draw and resolve Crossroads cards:

    1. The current player shuffles the Crossroads deck and deals each player 1 card.  
    2. Each player secretly looks at theirs, and selects 1 Trauma token to play facedown next to their Crossroads card. The indicated icon is the result that will be resolved. 

        - A player with no Trauma tokens may select either result when the card is revealed. 

    3. Once all players have made their selection, the current player reveals their card and token. The chosen result is resolved, and the next player reveals their choice. 

        - Resolve any executions normally when they occur, but the executed player's Crossroads card and token choice remain. If told that your turn is immediately over, that instruction will apply after the Crossroads phase is fully resolved. 
3. The Trial/Boxing the Line

    1. Remove all Crossroads cards and the Trauma tokens used with them from the game. Also remove all Ally cards and tokens, the Trauma tokens attached to them, and the Trauma tokens on board locations from the game. 
    2. Each player reveals all of their Trauma tokens. Human players discard their "Benevolent" tokens, while Cylon players discard all of their "Antagonistic" tokens.
    3. Each player with 2 or fewer tokens at this point discards them. 
    4. Each player counts the number of tokens they have remaining.
    5. If all players have 0 tokens, then no player is eliminated. If not, the player with the most tokens is eliminated from the game. 
        - If human players are tied, the President chooses one of the tied players for elimination.
        - If Cylon players are tied, each tied Cylon player is eliminated.
        - If both human and Cylon players are tied, follow *both* rules. All tied Cylons are eliminated, and the President chooses which tied human is eliminated. 

After the Crossroads phase is finished, resume play at the point that the Crossroads phase interrupted. If the Crossroads phase was reached via a jump, since the jump track was already reset do not reset it again (even though technically that is the next step in the procedure). The next jump will [end the game](#final-jump). 

#### Elimination

To eliminate a player from the game, follow the rules for execution, but do not choose a new character, and do not move to the Resurrection Ship. The eliminated player is entirely removed from the rest of the game. If they were the current player, the next player is now the current player. 

</div>

## Official variants

These come from the "optional rules" for the base game. The expansions don't always address these variants when they change the rules, so you might have to use your own judgement. 

### No Sympathizer

Most of the expansions also come with their own ways to avoid the  "Sympathizer" card, since it proved to be rather unpopular. These rules only apply in a 4 or 6 player game without a Cylon Leader and without Daybreak (since Daybreak mandates a Mutineer.)

To play with this variant: 

- Sharon "Boomer" Valerii is not allowed in a 6 player game. (There aren't enough "Not a Cylon" cards in the base game to accommodate her weakness along with Gaius, and part of her design is that she's more likely to be the Sympathizer. If you want, though, you can treat the Sympathizer card as the extra "Not a Cylon" card, or forbid using both Boomer and Gaius in the same game.)
- Follow the instructions for building the Loyalty deck as normal, but substitute a regular "Not a Cylon" instead of the "Sympathizer" card. 
- Start the game with 8 fuel, 7 food, 9 morale, and 10 population. 

In this variant, a revealed Cylon may draw 3 cards instead of 2. 

### Two player game

Both players choose any character they want, except "Boomer". Do not create a Loyalty deck during setup. Instead, during the Sleeper Agent phase, create a Loyalty deck of:

- 1 "You Are a Cylon" card
- 2 "Not a Cylon" cards
- 1 more "Not a Cylon" card for each resource that is in the red

Shuffle and deal 1 card to each player. 

### One player game

To set up, choose a character that is not "Boomer" and not Laura Roslin. This is your main character. Then choose another character as an "assist character". Build a Loyalty deck of 1 "You Are a Cylon", 6 "Not a Cylon" cards and deal 3 to the assist character. As the main character, you receive all the title cards. Start with 1 nuke instead of 2. 

Ignore all negative character abilities in this variant. 

The assist character does not get placed on the board, draw cards, take actions, or hold any titles. While playing the game, you may treat the assist character's once-per-game ability as if it were on your own character sheet. When receiving Skill cards, 1 is allowed to come from the assist character's Skill set (with a maximum of 5 total Skill cards). 

At the end of the "Activate Cylon Ships" step, if heavy raiders were activated during that step, reveal 1 of the assist character's Loyalty cards. If it is a Cylon card, resolve it and remove the assist character from the game. You can no longer use their Skill set or once-per-game ability. The "Can Damage Galactica" Loyalty card is instead resolved as "draw and resolve 2 Galactica damage tokens". The character that is targeted is always the main character. 

Always shuffle the assist character's Loyalty cards after they receive new ones, and after you look at any of them.

While in the Brig, movement is restricted as normal, but you still draw a Crisis card, and you may play up to 3 cards into a Skill check. 

You are allowed to choose yourself when playing Quorum cards that specify to choose "another" player. This does not apply to Skill cards, including "Executive Order". 

At the Sleeper Agent phase, add a "Not a Cylon" card to the Loyalty deck for each resource in the red. Then deal 4 Loyalty cards to the assist character and shuffle their cards. 


### Fully cooperative game

In this variant, there are no hidden Cylons. Do not build a Loyalty deck. The Admiral starts with just 1 nuke. Remove "Encourage Mutiny", "Release Cylon Mugshots", "Assign Arbitrator", and "Arrest Order" from the Quorum deck. Start with 8 fuel, 7 food, 9 morale, and 10 population. 

Each character must draw 1 less Skill card during the "Receive Skills" step. They may choose which card is not drawn.

Characters in the Brig still draw Crisis cards. 
