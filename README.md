# My Portfolio
This is a compilation of a small porfolio I've developed during my journey.

## Recent PowerApp Examples
<p align="center">
  <img src="https://github.com/user-attachments/assets/58973cbe-ea3c-411d-a58d-3189beda5df6" width="32%" />
  <img src="https://github.com/user-attachments/assets/48e7b4fc-caae-4b73-9c0e-ddee1add63ef" width="32%" /> 
  <img src="https://github.com/user-attachments/assets/d4d30f5c-bc7d-439d-bc1e-db4bf4092433" width="32%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/29d8b197-e3bd-4c5a-b440-930262d92f5b" width="32%" />
  <img src="https://github.com/user-attachments/assets/954f9226-0eb5-41ce-b08f-8cbcf8aad125" width="32%" />
  <img src="https://github.com/user-attachments/assets/05673950-d32a-4df9-b016-07509f3cef05" width="32%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/5c08303b-3d4c-479f-a248-7202456505ee" width="32%" />
  <img src="https://github.com/user-attachments/assets/6bdc157b-22cb-4c47-afcd-2177476568d2" width="32%" />
  <img src="https://github.com/user-attachments/assets/9b1a9e89-d13a-4d8c-b0a2-3e0193d9a7c3" width="32%" />
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/45d96112-a1d5-41ce-ac61-80adb989593a" width="32%" />
  <img src="https://github.com/user-attachments/assets/e06d4b05-3917-4de8-89fc-99d2dce245db" width="32%" />
  <img src="https://github.com/user-attachments/assets/8b84ff79-6e0f-485a-89c0-273bacfa8277" width="32%" />
</p>

## 4 Percentage Slider Code Example
<p align="center"><img width="60%"  alt="2026-01-12 12_56_37-Settings" src="https://github.com/user-attachments/assets/1e8057d6-c798-41c1-be20-292016f77819" /></p>


I built a custom algorithm to manage four connected sliders that adjusts the total usable value per slider. The challenge was ensuring that when a user moves one slider, the others adjust proportionally rather than just cutting off the next available value. I also built a 'locking' feature, allowing users to pin specific values while the remaining sliders auto-balance around them.
<details>
<summary><b>Click to view the Slider Logic Code</b></summary>
  
```powerapps
If(
    !updating,
    Set(updating, true);

    With({
        // 1. Capture Inputs and calculate Locking constraints
        // We use With() to create local variables, avoiding global Set() clutter
        reqVal: Min(100, Max(0, Round(Slider2_4.Value, 0))),
        wLockLLC: LockLLC,
        wLockSEL: LockSEL,
        wLockOPAL: LockOPAL
    },
    With({
        // 2. Calculate the Locked Total (Values that cannot move)
        lockedTotal: (If(wLockLLC, LLCSliderpct2, 0) + If(wLockSEL, SELSliderpct3, 0) + If(wLockOPAL, OPALSliderpct4, 0))
    },
    With({
        // 3. Determine the new value for the active slider (PDMS)
        newPDMS: Min(reqVal, 100 - lockedTotal)
    },
    With({
        // 4. Calculate Remainder and Eligible Weights for distribution
        rem: 100 - newPDMS - lockedTotal,
        // Only get current values if they are UNLOCKED. Otherwise weight is 0.
        wLLC: If(!wLockLLC, LLCSliderpct2, 0),
        wSEL: If(!wLockSEL, SELSliderpct3, 0),
        wOPAL: If(!wLockOPAL, OPALSliderpct4, 0)
    },
    With({
        // 5. Sum of weights to determine ratios
        weightSum: wLLC + wSEL + wOPAL
    },
    With({
        // 6. Calculate New Values (The Logic Core)
        // If weights exist (>0), distribute proportionally.
        // If weights are 0 (all unlocked are 0), fill the first available unlocked slider.
        calcLLC: If(weightSum > 0, 
                    Round(rem * (wLLC / weightSum), 0), 
                    If(!wLockLLC, rem, 0)
                  ),
        calcSEL: If(weightSum > 0, 
                    Round(rem * (wSEL / weightSum), 0), 
                    If(!wLockLLC, 0, If(!wLockSEL, rem, 0))
                  )
        // We do not calculate OPAL here; we will use subtraction later to ensure 100% match
    },
        // 7. Execute Updates
        // Apply final values. For the last item (OPAL), we subtract the others from 'rem' to catch rounding errors.
        Set(PDMSSliderpct1, newPDMS);
        Set(LLCSliderpct2, If(wLockLLC, LLCSliderpct2, calcLLC));
        Set(SELSliderpct3, If(wLockSEL, SELSliderpct3, calcSEL));
        Set(OPALSliderpct4, If(wLockOPAL, OPALSliderpct4, rem - calcLLC - calcSEL));

        // 8. Reset Sliders
        // Use Concurrent for better performance. Only reset if the value actually changed.
        Concurrent(
            If(!wLockLLC && LLCSliderpct2 <> calcLLC, Reset(Slider2_5)),
            If(!wLockSEL && SELSliderpct3 <> calcSEL, Reset(Slider2_6)),
            If(!wLockOPAL && OPALSliderpct4 <> (rem - calcLLC - calcSEL), Reset(Slider2_7))
        )
    ))))));

    Set(updating, false)
)
```
</details>

## Data Analytics
[Link to slides my data analysis on World of Warcraft](Data_Manipulation.pdf)

![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/6eb079d6-73e4-44b2-af62-049588c3496a)

Includes various graphs and code on visualisations all done in "R"


## Most recent project (Download Git Repo To Play)
My second, the game I put in the most amount of work into is in this git repo which features a 2D Platformer game to show off how a modern Metroidvania game plays out from start to finish.
![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/52b296cf-3e0b-45c2-baff-ee3c7c11bb7f)
![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/7f329901-12d1-471c-bbed-b8b17e315875)

## My first game, in pure Python
As I don't have the files anymore I still have a youtube video of the game which everything was built from scratch as well as importing sprites by hand and adjusting them by pixel.
### Click for video demonstration
[![Python Game](https://img.youtube.com/vi/TjiNKc-bfKg/hqdefault.jpg)](https://www.youtube.com/watch?v=TjiNKc-bfKg)

# General Applications
## Spotify App Integration into a new app
### Click for video demonstration
[![Spotify App](https://img.youtube.com/vi/5p6KBzC5My0/hqdefault.jpg)](https://youtu.be/5p6KBzC5My0)

## PowerApps Projects
[A report with more examples and work I have done in the Power Platform](https://1drv.ms/u/s!AlofhCYB4B9ilCMMzFNT5zI8mUZd?e=ndYeyk)

![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/4a372c66-5762-4d08-bf8c-00e398a81a8f)

![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/f192d977-13c1-4004-a8c7-ccddbcde915b)

![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/3f3b6d30-2acf-4565-b0c7-62084c6a5499)

![image](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/65d85c26-3f24-41da-8ec3-8b7ef1fda133)

![2022-07-18 15_59_47-Window](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/cbcf4d40-9e81-40c3-814e-b9c75c48b805)

![2022-07-18 16_12_23-Edit your flow _ Power Automate - Opera](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/fbe5aea2-40c0-4580-962e-a443702d950d)

![2022-07-18 17_38_38-Power Apps - Opera](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/4c2fdfe3-3ec6-4a80-9423-b5a1a9d5ff8a)

![2022-07-18 17_38_49-Power Apps - Opera](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/7f2a9bbe-4a5e-4471-87aa-0a4c0d8d59f3)

![2022-07-18 17_39_16-Power Apps - Opera](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/1be0bacd-9c30-4037-b3cd-90ffae5e7faf)

![2022-07-18 17_39_52-Power Apps - Opera](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/2115e356-372e-4890-8aeb-09d0a072634c)

![2022-07-18 17_40_12-Power Apps - Opera](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/e7061bb6-5785-4ceb-9123-674f02a546a7)

![2022-07-21 19_00_46-Window](https://github.com/RolandasLeon/GameDevPortfolio/assets/72037048/01c7e6fa-51bd-4ed7-a99d-7239bbc9a55f)


