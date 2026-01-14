### Notice Before Install

* UNMAINTAINED.

* Latest Version: 2.90.0   Update Time: 2018-06-15

* Any pull requests are appreciated.

<!-- TOC -->

- [Notice Before Install](#notice-before-install)
- [Screenshots](#screenshots)
- [About Font](#about-font)
  - [Font Preference (for reference only)](#font-preference-for-reference-only)
- [Customize Condition](#customize-condition)
  - [Comparison Value](#comparison-value)
  - [Example](#example)
  - [Skill/Item ID Table](#skillitem-id-table)
  - [Buff Image Table](#buff-image-table)
- [Attack Rule Example](#attack-rule-example)
- [Question and Answer](#question-and-answer)
- [Changelog](#changelog)

<!-- /TOC -->
***

### Screenshots

![hvAutoAttack_Setting](https://github.com/dodying/UserJs/raw/master/HentaiVerse/hvAutoAttack/hvAutoAttack_Setting.png)

***

### About Font

Scripts get information through text, and if you have not yet modified the font, some information may not be available, causing some errors to occur.

#### Font Preference (for reference only)

1. font-family: Times New Roman
2. font-size: 12
3. font-weight (normal, bold, bolder, lighter): normal
4. font-style (normal, italic, oblique): normal
5. vertical adjust: -5

***

### Customize Condition


Each area with a red dotted border can be set to a customize condition.

Customizable Formula is support now, such as `hp > mp` or `2 * ( hp + mp ) > sp`, supported operators: `+` `-` `*` `/` `%` `&&` `||` `!` `>` `<` `>=`(`≥`) `<=`(`≤`) `==`(`=`,`===`) `!=`(`≠`,`~=`,`<>`), logical operators returns 0 or 1 (as false or true)

* If these areas are left blank (a condition is not set), then it's equivalent to true.

When the mouse moves in these areas, a box is displayed in the upper right corner. (When the mouse out, the box disappears)

Four drop down lists and one button are visible in the box

* Drop-down List 1: the location of this condition inserted (see the example for specific effects)

* Drop-down List 2/4: comparison value A / comparison value B

* Drop-down List 3: operator

* Button ADD: Generates an input box with a value of `A Operator B` 

* Legacy version condition such as `A,Comparison-Operator,B` is still supported, Comparison-Operator: (`1`: >, `2`: <, `3`: ≥, `4`: ≤, `5`: =, `6`: ≠)

#### Comparison Value

1. `hp`/`mp`/`sp`: **percent** of hp/mp/sp, 0-100
2. `oc`: Overcharge, 0-250
3. `monsterAll`/`monsterAlive`/`bossAll`/`bossAlive`: amount of all monster/boss (alive)
4. `roundNow`/`roundAll`/`roundLeft`
5. `isRoundType`、`ar`、`ba`、`iw`、`tw`、`gr`、`rb`: is current round type as the target type, such as: both `_isRoundType_ar` and `_ar` returns `is currently in The Arena`
6. `roundType`: Battle Type (`ar`: The Arena, `rb`: Ring of Blood, `gr`: GrindFest, `iw`: Item World, `ba`: Random Encounter, `tw`: The Tower)

  (**Note**: Comparison between strings will automatically remove the outermost quotation marks, such as `"ar"`/`'ar'` is as same as `ar`

7. `attackStatus`: Attack Mode (`0`: Physical, `1`: Fire, `2`: Cold, `3`: Elec, `4`: Wind, `5`: Divine, `6`: Forbidden). Or use `_phys`, `_fire`, `_cold`, `_elec`, `_wind`, `_divi`, `_forb` as `if current attack mode is ...`, such as `_phys` equals `attackStatus == 0`。
   - Value acquired above is the default Attack Mode, to get current Attack Mode after calculating Secondary Attack Mode, use suffix `Cur` ( and prefix `_` for `attackStatus`), such as：`_attackStatusCur`,`_physCur`, `_fireCur`, `_coldCur`, `_elecCur`, `_windCur`, `_diviCur`, `_forbCur`
     - current value getting by:
       - During `Secondary Attack`, return `current attempting mode` 
         - If configed any current condition in `Secondary Attack Mode`, returns `mode that the condition is in`. Such as: In conditions of `Attack mode Fire`, `_fireCur` is as `true`，`_windCur` is as `false`
       - In other options, attack will be simulated until the spell is callable ( cache the successfully simulated mode into a temp variable and returns it)
8. `fightingStyle`: Fighting Style (`1`: Niten, `2`: 1H, `3`: 2H, `4`: DW, `5`: Staff). Or use `_nt`, `_1h`, `_2h`, `_dw`, `_staff` as `if current fighting style is ...`, such as `_nt` equals `fightStyle == 1`
9. `isCd`: whether the skill/item is cooldowning, format: `_isCd_id`

  **example 1**: the id of Protection is 411 , `!_isCd_411` means Protection can't be casted or `_isCd_411` means Protection can be casted

  **example 2**: the id of ManaElixir is 11295, `!_isCd_11295` means ManaElixir can't be used or `_isCd_11295` means ManaElixir can be used

10. `buffTurn`: time the buff last in person, format`_buffTurn_img`

  **example**: the image of Protection is protection, `_buffTurn_protection == 0` means you don't have the buff of Protection or `_buffTurn_protection >= 10` means the buff of Protection on you last at least 10 turns

11. `_targetHp`/`_targetMp`/`_targetSp`/`_targetBuffTurn`/`_targetRank`: `HP%`/`SP%`/`MP%`/`buffRemainTime`/`attackRank` of target monster
    1. ,  suffix of `_targetBuffTurn_` is same as 8.`buffTurn`（such as：`_targetBuffTurn_bleed != 0` means remain turns of bleed buff on target monster is not equal to 0. Target that is calculating is chosen by following rules:
        1. The highest priority monster by rank in default situations.
        2. Weapon skills (OFC, T1~T3, etc.), Offensive Spell skills (Tire2, Tire3): by each condition > for each ranked target > find the target fit all sub-condition in the condition and cast to it. Such as the pic below: condition for Merciful Blow: only cast to targets which with hp below 25% and a bleed buff.
    
        ![example](https://github.com/user-attachments/assets/da181eac-e634-41ad-97a7-ff59a7b28b6d)
    
    2. `_targetBuffTurn` returns the value as same as the ranked order given by Attack Rule (0~9, smaller number as higher priority)
12. `targetName`/`targetBossType`: name and boss type for target monster
    1. `_targetName` returns a string of the target name (**Note**: Comparison between strings will automatically remove the outermost quotation marks, meanwhile **please replace space` ` with underline`_`**, such as `Yugi_Nagato`/`'Yugi_Nagato'`/`"Yugi_Nagato"`)
    2. `_targetBossType` is determined by name:
        1. `Manbearpig`、`White Bunneh`、`Mithra`、`Dalek`: 1 (BOSS)
        2. `Konata`、`Mikuru Asahina`、`Ryouko Asakura`、`Yuki Nagato`: 2 (Legendaries)
        3. `Skuld`、`Urd`、`Verdandi`、`Yggdrasil`: 3 (Trio and the Tree)
        4. `Rhaegal`、`Viserion`、`Drogon`: 4 (A Dance with Dragons)
        5. `Real Life`、`Invisible Pink Unicorn`、`Flying Spaghetti Monster`: 5 (Gods)
        6. others: 0 (not boss)

13. blank: the value you want to put in

#### Example

![example](https://github.com/dodying/UserJs/raw/master/HentaiVerse/hvAutoAttack/hvAutoAttack_CustomizeCondition.png)

In the picture, I set three big conditions (2 contains two small conditions)

1. Condition 1: total rounds more than 12

2. Condition 2: bosses more than 1 and hp more than hp

3. Condition 3: monsters more than 6

It's TRUE, when any big condition is true (To judge big condition is true, all small condition must true)

The following is a schematic diagram of the circuit diagram

![schematic diagram](https://github.com/dodying/UserJs/raw/master/HentaiVerse/hvAutoAttack/hvAutoAttack_CustomizeConditionCircuit.png)

#### Skill/Item ID Table

| 1 | 2 | 3 |
| - | - | - |
| Flee / 1001 | - | - |
| Scan / 1011 | - | - |
| FUS RO DAH / 1101 | - | - |
| Orbital Friendship Cannon / 1111 | - | - |
| Skyward Sword / 2101 | - | - |
| Shield Bash / 2201 | Vital Strike / 2202 | Merciful Blow / 2203 |
| Great Cleave / 2301 | Rending Blow / 2302 | Shatter Strike / 2303 |
| Iris Strike / 2401 | Backstab / 2402 | Frenzied Blows / 2403 |
| Concussive Strike / 2501 | - | - |
| Fiery Blast / 111 | Inferno / 112 | Flames of Loki / 113 |
| Freeze / 121 | Blizzard / 122 | Fimbulvetr / 123 |
| Shockblast / 131 | Chained Lightning / 132 | Wrath of Thor / 133 |
| Gale / 141 | Downburst / 142 | Storms of Njord / 143 |
| Smite / 151 | Banishment / 152 | Paradise Lost / 153 |
| Corruption / 161 | Disintegrate / 162 | Ragnaro / 163 |
| Drain / 211 | Weaken / 212 | Imperil / 213 |
| Slow / 221 | Sleep / 222 | Confuse / 223 |
| Blind / 231 | Silence / 232 | MagNet / 233 |
| Cure / 311 | Regen / 312 | Full-Cure / 313 |
| Protection / 411 | Haste / 412 | Shadow Veil / 413 |
| Absorb / 421 | Spark of Life / 422 | Spirit Shield / 423 |
| Arcane Focus / 431 | Heartseeker / 432 |  |
| - | - | - |
| Health Draught / 11191 | Health Potion / 11195 | Health Elixir / 11199 |
| Mana Draught / 11291 | Mana Potion / 11295 | Mana Elixir / 11299 |
| Spirit Draught / 11391 | Spirit Potion / 11395 | Spirit Elixir / 11399 |
| Energy Drink / 11401 | - | - |
| Last Elixir / 11501 | - | - |
| Infusion of Flames / 12101 | Infusion of Frost / 12201 | Infusion of Lightning / 12301 |
| Infusion of Storms / 12401 | Infusion of Divinity / 12501 | Infusion of Darkness / 12601 |
| Scroll of Swiftness / 13101 | Scroll of Protection / 13111 | Scroll of the Avatar / 13199 |
| Scroll of Absorption / 13201 | Scroll of Shadows / 13211 | Scroll of Life / 13221 |
| Scroll of the Gods / 13299 | - | - |
| Flower Vase / 19111 | Bubble-Gum / 19131 | - |

#### Buff Image Table

| 1 | 2 | 3 |
| - | - | - |
| - | Regen / regen | - |
| Protection / protection | Haste / haste | Shadow Veil / shadowveil |
| Absorb / absorb | Spark of Life / sparklife | Spirit Shield / spiritshield |
| Arcane Focus / arcanemeditation | Heartseeker / heartseeker | Cloak of the Fallen / fallenshield |
| Health Draught / healthpot | Mana Draught / manapot | Spirit Draught / spiritpot |
| Infusion of Flames / fireinfusion | Infusion of Frost / coldinfusion | Infusion of Lightning / elecinfusion |
| Infusion of Storms / windinfusion | Infusion of Divinity / holyinfusion | Infusion of Darkness / darkinfusion |
| Scroll of Swiftness / haste_scroll | - | - |
| Flower Vase / flowers | Bubble-Gum / gum | - |
| Sleep / sleep | Blind / blind | Slow / slow |
| Imperil / imperil | MagNet / magnet | Silence / silence |
| Drain / drainhp | Weaken / weaken | Confuse / confuse |
| Coalesced Mana / coalescemana | Stunned / stun / wpn_stun | Penetrated Armor / ap / wpn_ap |
| Bleeding Wound / bleed / wpn_bleed | Absorbing Ward / absorb | Fury of the Sisters / trio_furyofthesisters |
| Lamentations of the Future / trio_skuld | Screams of the Past / trio_urd | Wailings of the Present / trio_verdandi | Searing Skin / firedot | Freezing Limbs / coldslow |
| Turbulent Air / windmiss | Deep Burns / elecweak | Breached Defense / holybreach |
| Blunted Attack / darknerf | Burning Soul / soulfire | Ripened Soul / ripesoul |

***

### Attack Rule Example

| Enemy id | now hp | init PW | Imperiled (-2) | Drained (-1) | Confused (+2) | PW |
| - | - | - | - | - | - | - |
| 1 | 20K | 10 | √ | | √ | 10 |
| 2 | 30K | 15 | | √ | | 14 |
| 3 | 40K | 20 | √ | | | 18 |

**NOTE**: The script will attack enemy who has the least PW first.

In this example, the script will attack enemy 1 next.

***

### Question and Answer

* Q: [Alarm not autoplaying on Chrome](https://github.com/dodying/UserJs/issues/31)

  A: Open `chrome://flags/#autoplay-policy` and select `No user gesture is required`

***

### Changelog

* Latest
1. Follow [History for hvAutoAttack - github](https://github.com/dodying/UserJs/commits/master/HentaiVerse/hvAutoAttack/hvAutoAttack.user.js)

* Old
1. see [README_Chinese#更新历史](https://github.com/dodying/UserJs/blob/master/HentaiVerse/hvAutoAttack/README.md#更新历史)













