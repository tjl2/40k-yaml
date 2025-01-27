# Kill Team YAML File Notes

Things to note when building YAML files for Kill Teams

*TODO: Replace with a schema one day…*

## Firefight Ploys

Firefight ploys have a `when` attribute, which summarises when the ploy can be used. Options for this attribute are:

- `activation` - at some point when activating a friendly operative; the description will give more details on whether that is before, during or after a specific action.
- `shooting` - when a friendly operative is carrying out a shoot action.
- `fighting` - when a friendly operative is carrying out a fight action.
- `roll defence dice` - when an enemy operative is shooting a friendly operative.
- `conteracting` - when a friendly operative is counteracting.
- more to come…

## Abilities

Operatives' abilities have a `when` attribute, which summarises when the ability can be used.

- 'select operatives'
- 'strategy phase'
- 'firefight phase'

## Weapons

The list of available weapons is divided into "`ranged_weapons`" and "`melee_weapons`". Often, the operative will not have access to all of these weapons, therefore `weapon_options` should be used to define the potential choices.

For weapons that have multiple profiles (e.g.: Plasma Pistol (Standard), Plasma Pistol (Supercharge)), they are added as two separate profiles. When listing these in `weapon_options` just the main weapon name (e.g.: 'Plasma Pistol') should be used to include all profiles.

## Weapon options

If an operative has no `weapon_options` attribute, then all the weapons under `ranegd_weapons` and `melee_weapons` are used.

For operatives where you must choose equipment options, these are listed as `weapon_options.choice_sets`. A choice set contains a list of `options`, with each option containing a list of one or more objects. This object contains a `type` (these will usually be `"ranged"` or `"melee"`) and a list of `choices`; one choice for each type can then be chosen.

For example, the options for an **Angels of Death** **Intercessor Sergeant** are written as follows:

> … one option from each of the following:
> * Auto bolt rifle, bolt rifle or stalker bolt rifle
> * Chainsword, fists, power fist, power weapon or thunder hammer

This is modelled in the YAML like so:

```yaml
operatives:
  - name: Intercessor Sergeant
    weapon_options:
      - option_group:
        - ranged_choices:
          - auto bolt rifle
          - bolt rifle
          - stalker bolt rifle
        - melee_choices:
          - chainsword
          - fists
          - power fist
          - power weapon
          - thunder hammer
```

The operative will then pick one item from each of the `ranged_choices` and `melee_choices` lists within that `option_group` attribute of the `weapon_options` attribute.

Looking at the above example, it could be argued that the `options` object is redundant, as there is only one. However, there are times where another option can be available (possibly due to GW model configurations, I suspect). For another example that shows this, the **Assault Intercessor Sergeant's** equipment options are worded as:

> … one option from each of the following:
> * Hand flamer or heavy bolt pistol
> * Chainsword, power fist, power weapon or thunder hammer
> Or the following option:
> * Plasma pistol; chainsword

This is written in the YAML as follows:
```yaml
operatives:
  - name: Assault Intercessor Sergeant
    weapon_options:
      - option_group:
        - ranged_choices:
          - hand flamer
          - heavy bolt pistol
        - melee_choices:
          - chainsword
          - power fist
          - power weapon
          - thunder hammer
      - option_group:
        - ranged_choices:
          - plasma pistol
        - melee_choices:
          - chainsword
```

You can see how this differs from the first example as we have a second `option_group` attribute within the `weapon_options`. This shows how you can opt for just the **Plasma Pistol** & **Chainsword** combination. Additionally, this shows that you could choose a **Hand Flamer** and a **Thunder Hammer**, but not a **Plasma Pistol** and **Power Fist**, as they are from 2 different option groups.

## Operative Groups

In the YAML, the `operative_groups` object is modelled in a way that shows which operatives can be taken in the Kill Team. Your Kill Team will have a `total_operatives` value and each `operative_group` will have a `total_operatives` value too (the latter should always add up to match the former - unless there are some weird Kill Teams out there that I haven't encountered that break this rule).

Operatives are listed for these groups, under an attribute that indicates what rule exists for their inclusion:

* `exclusive_one_of`: you can only take one of the operatives from this list
* `limit_one_of`: you can take more than one operative from this list, but only one of each
* `any_number_of`: you can have zero or more of any of the operatives in this list, limited only by the group's `total_operatives`

So as an example in the **Angels of Death** Kill Team:

```yaml
operative_groups:
  - name: leader
    total_operatives: 1
    exclusive_one_of:
    - Space Marine Captain
    - Assault Intercessor Sergeant
    - Intercessor Sergeant
  - name: team
    total_operatives: 5
    exclusive_one_of:
    - Heavy Intercessor Gunner
    - Eliminator Sniper
    limit_one_of:
    - Assault Intercessor Grenadier
    - Intercessor Gunner
    any_number_of:
    - Assault Intercessor Warrior
    - Intercessor Warrior
```

From this, we can see that

* we must choose between the 'leaders' and *only have one* of; '**Space Marine Captain**', *or* '**Assault Intercessor Sergeant**', *or* '**Intercessor Sergeant**'
* we can have *zero or one* of '**Heavy Intercessor Gunner**' *or* '**Eliminator Sniper**'
* we can have *zero or one* **'Assault Intercessor Grenadier**' ***and*** *zero or one* '**Intercessor Gunner**'
* we can have *multiple* '**Assault Intercessor Warrior**' *and* '**Intercessor Warrior**' operatives to fill the rest of the spots on the team

Of course, none of these naming conventions state that you *must* pick any operatives from the list, but not doing so will not get you a valid total number of operatives in your Kill Team.
