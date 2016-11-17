---
title: "Xbox One Backwards Compatibility With The Xbox 360"
runtime: shiny
output: html_document
---
## Background
I am a huge fan of the Xbox and Microsoft has made a lot of money off of me since they made the Xbox One backwards compatible with Xbox 360 games. I have a back catalog of games I've bought through Microsoft's weekly sales that has now put my Steam catalog to shame ([Steam Analysis](http://blog.nycdatascience.com/student-works/factors-affecting-sales-steams-2016-summer-sale/)). My problem is that during these sales, sometimes it's hard to guess which ones may become backwards compatible in the future. Last Friday was the one year anniversary of the backwards Compatibility update and I felt compelled to use my skills as a Data Scientist to make something fun, and hopefully useful.

For those that are unaware, Microsoft has made many Xbox 360 games playable on the Xbox One. They have been making a few games a week compatible because it takes time to test that their software layer works properly with the game and they must get publishers and rights holders to agree to allow their games to be made Compatible. Publishers may not want certain games to be available for many reasons including complicated rights, timing, and remakes or remasters that may lose sales. Microsoft has also decided to not make Xbox 360 games that require the system's Kinect or other special peripherals (microphones, guitars, skateboards) compatible.

## Scraping
Microsoft surely takes many factors into account when prioritizing their games, but I needed data to analyze so I could think more like them. I decided to collect data from websites using Scrapy, a free framework that plugs into Python. I first designed a "spider" to grab MajorNelson's backwards Compatibility list off of his blog because I figured it would be quickest to be updated. I then scraped game names, user and professional scores from Metacritic along with several other factoids like release dates in case another site is lacking. I know Microsoft has said in the past that they look at UserVoice votes to help make choices, which I confess I was skeptical of, and grabbed the votes and forum comment counts. Next, I grabbed the names and differentiations of Xbox 360 exclusives (console, regular) and Kinect games (required, supported) from Wikipedia. Remember that this is important because Microsoft says they won't support Kinect required games but games with optional Kinect functionality left me wondering. I pondered if also being on Xbox One in the form of remasters, remakes or cross-platform releases had much of an effect. For reference, I started with the list of Xbox One games off Microsoft's site and I even scraped a GameInformer article that gets regularly updated with the latest remasters. Based off these and further research, I made a list of games that could be found on both platforms. Most importantly, I grabbed the list of all Xbox 360 games and extracted every single piece of information I could find on their pages including publishers, developers, user review scores and their counts, release dates, Smartglass features, demo availability, multiplayer types, sound features, and the number of add-ons, themes, gamerpics and the like. After scraping these 8 sites, I felt ready to start processing the data into one master dataset. 


#### What I'd Like to Add
What I would still like to gather is the game's engine but I haven't looked to see if there was any consistency between Wikipedia's individual game pages. Additionally. going to each Xbox 360 game's page may give me a more in depth look at other platforms these games are available on, their release countries, updated publishers in case I got a game's current publishing rights wrong and more. I also would like to make sure I got all the remasters and games that required peripherals. I also note that certain publishers no longer are operational and should incorporate that into the data. I wouldn't be surprised to see some mistakes or missing attributes though I tried hard to avoid it. However, that can be fixed with time and the data is easy to update.

## Making the Ultimate Dataset
I used R for its ability to easily manipulate and organize data. I imported all the files into data frames from the .csv files I had made.

#### Finding more info in the data
I looked for ways I could split the data even further. Examples of this is splitting console exclusive and regular exclusives into unique columns, seperating multiplayer capabilities and filtering Uservoice labels into "In progress" and "Closed"" categories which I further seperated based on the reason the game had been closed.

#### Merging the data
The most time consuming part of this project involved the game's names. 
+ The names of games on Microsoft's sites were abbreviated or shortened due to character limits on their own website or are only available as "XYZ Edition"
+ Users often used abbreviations or modified names when submitting to uservoice
+ Wikipedia articles sometimes used the international name of a game
+ Punctuation, trademark/copyright marks, and differences in spelling prevent one to one matching

The solution was to make a new column of names where I would remove as many obstacles as I could so that when datasets are merged, there are the fewest differences possible. Through [my past experiences](http://blog.nycdatascience.com/student-works/shining-light-darknet/) with more difficult datasets, I knew this would require the use of my regular expression skills. I began by removing words including "the," "edition," "special," "full game," "free to play" and many more. I then removed pluralization and possession from words and converted numbers to roman numerals ("Assassin's Creed 2 to "Assassin Creed II". I also removed all non alphanumeric letters as well as spacing and capitalization. For many games, the differences were too great and had to be renamed before any of these processes were done ("COD:MW2" to "Call of Duty: Modern Warfare 2"). 

This is the list I used to convert the remaining games. I included this because it was a labor of love.


```r
gameNameDict = c('Call of Duty: Modern Warfare 2','Call of Duty: Modern Warfare 3','Call of Duty 4: Modern Warfare','Battlefield: Bad Company 2','Call of Duty: Black Ops II','Dead Rising','Halo 3: ODST','Halo: Combat Evolved Anniversary', 'Lost Planet 2','Need for Speed: ProStreet','Plants vs Zombies: Garden Warfare','Resident Evil 5','World of Tanks','Tom Clancy\'s Ghost Recon Advanced Warfighter 2','Assassin\'s Creed: Revelations','Samurai Shodown: Sen','Prototype','Ace Combat: Assault Horizon','Battlefield: Bad Company','Dynasty Warriors 6','Dynasty Warriors 6 Empires','DmC: Devil May Cry','Pac-Man and the Ghostly Adventures','Pac-Man and the Ghostly Adventures 2','Assassin\'s Creed IV: Black Flag','Zone of the Enders HD Collection','Dance Evolution','Blackwater','Ace Combat 6: Fires of Liberation','State of Decay','Army of Two: The 40th Day','Condemned: Criminal Origins','Lumines Live!','Bangai-O HD: Missile Fury','Banjo-Kazooie: Nuts & Bolts','Blackwater','Cabela\'s African Adventures','Cabela\'s Big Game Hunter 2010','Cabela\'s Dangerous Hunts 2011','Cabela\'s North American Adventure','Cabela\'s Survival: Shadows of Katmai','Viva Pinata: Trouble in Paradise','Life Is Strange','Marlow Briggs and the Mask of the Death','Fable II','Fable III','Falling Skies: The Game','Feeding Frenzy 2: Shipwreck Showdown','Luxor 2','The Elder Scrolls IV: Oblivion','The Elder Scrolls V: Skyrim','Grand Theft Auto V','Grand Theft Auto IV', 'Blacksite: Area 51','BlazBlue: Calamity Trigger','BlazBlue: Continuum Shift','Blazing Angels 2: Secret Missions of WWII','Blazing Angels: Squadrons of WWII','2010 FIFA World Cup South Africa','3D Ultra MiniGolf Adventures','Adventure Time: Explore the Dungeon Because I DON\'T KNOW!','Alien Breed: Evolution','Avatar: The Last Airbender -- The Burning Earth','Beijing 2008 - The Official Video Game of the Olympic Games','Bejeweled 2 Deluxe','Ben 10 Ultimate Alien: Cosmic Destruction','Beowulf: The Game','Bomberman Live: Battlefest','Brutal Legend','Bully: Scholarship Edition','Cabela\'s North American Adventures','America\'s Army: True Soldiers','Brothers in Arms: Hell\'s Highway','Castlevania: Lords of Shadow 2','Castlevania: Lords of Shadow','Castlevania: Harmony of Despair','Cloudy With a Chance of Meatballs','Tom Clancy’s Rainbow Six Vegas','Tom Clancy’s Rainbow Six Vegas 2','James Cameron\'s Avatar','James Cameron\'s Avatar','Transformers: War for Cybertron','Asteroids & Deluxe','Viva Pinata: Trouble in Paradise','Tom Clancy\'s Splinter Cell Double Agent','Armored Core for Answer','Every Extend Extra Extreme','Geometry Wars Retro Evolved','Hydro Thunder Hurricane','Kameo: Elements of Power','Monkey Island 2 Special Edition: LeChuck\'s Revenge','Monkey Island: Special Edition','Operation Flashpoint: Dragon Rising','Watchmen: The End is Nigh','Watchmen: The End is Nigh Part 2','Phantom Breaker:Battle Grounds','Zone of the Enders HD Collection','Puzzle Quest: Challenge of the Warlords','BIT.TRIP Presents… Runner2: Future Legend of Rhythm Alien',"Sam & Max Beyond Time & Space",'Sonic The Hedgehog 4 Episode II','Soul Calibur II HD','Jane\'s Advanced Strike Fighters','Tour de France 2009','Tour de France 2013',"50 Cent: Blood on the Sand",'Brave: The Video Game','Battlefield 2: Modern Combat','Dragon Ball Z for Kinect','Dragon Ball Z: BURST LIMIT','LEGO Indiana Jones 2: The Adventure Continues','LEGO Indiana Jones: The Original Adventures','LEGO Star Wars III: The Clone Wars','LEGO The Lord of the Rings','LEGO Harry Potter: Years 1-4','The Walking Dead: Survival Instinct','Transformers: Revenge of the Fallen','The Warriors: Street Brawl','The Raven - Legacy of a Master Thief Episode 1','Metal Gear Solid HD Collection','Metal Gear Solid HD Collection','The Witcher 2: Assassins of Kings','Tony Hawk\'s American Wasteland','A.R.E.S. Extinction Agenda EX','Sonic\'s Ultimate Genesis Collection','Tom Clancy\'s Splinter Cell Conviction','World Series of Poker: Tournament of Champions','Where the Wild Things Are','Titanfall','Worms Revolution','Valiant Hearts: The Great War','Young Justice: Legacy','Zeno Clash Ultimate Edition','Thrillville: Off the Rails','The Price is Right: Decades','Penguins of Madagascar: Dr. Blowhole Returns - Again','The King of Fighters 2002 Ultimate Match','Tom Clancy\'s H.A.W.X','Star Wars: The Clone Wars - Republic Heroes','Superstars V8 Next Challenge','Street Fighter II Hyper Fighting','Dead or Alive 5 Last Round','Super Hero Squad: The Infinity Gauntlet','Are You Smarter Than a 5th Grader: Game Time','Are You Smarter Than a 5th Grader: Game Time',"Cabela's Alaskan Adventures",'Deadliest Catch: Alaskan Storm','Hannah Montana The Movie','Ace Combat: Assault Horizon','Chronicles of Riddick: Assault on Dark Athena','Naval Assault: The Killing Tide','Cabela\'s African Safari','Chivalry: Medieval Warfare','Civil War: Secret Missions','Call of Duty: Black Ops III','Cabela\'s Big Game Hunter: Hunting Party','Command & Conquer 3: Kane\'s Wrath','Command & Conquer Red Alert 3','Commanders: Attack of the Genos','Batman: Arkham Origins Blackgate - Deluxe Edition','Zero D Beat Drop','Bakugan: Defenders of the Core','Bakugan Battle Brawlers','Dance Dance Revolution Universe','Dance Dance Revolution Universe 2','Dance Dance Revolution Universe 3','Pirates of the Caribbean: At World\'s End','UEFA Champions League 2006-2007','Zeit Squared','WWE Legends of WrestleMania','007: Quantum of Solace','Nickelodeon Teenage Mutant Ninja Turtles','Bass Pro Shops: The Strike','Dynasty Warriors: Strikeforce','Strania - The Stella Machina -','Naruto Shippuden: Ultimate Ninja Storm Generations','Bladestorm: The Hundred Years\' War','Naruto Shippuden: Ultimate Ninja Storm 3','Naruto Shippuden: Ultimate Ninja Storm 2','Naruto Shippuden: Ultimate Ninja Storm 3 Full Burst','Naruto Shippuden: Ultimate Ninja Storm 2','Naruto Shippuden: Ultimate Ninja Storm 3 Full Burst','SpongeBob\'s Truth or Square','SpongeBob Sparepants: Underpants Slam!','SpongeBob Sparepants: Underpants Slam!','Star Ocean: The Last Hope','High School Musical 3: Senior Year Dance','DeathSpank: Thongs of Virtue','Digimon: All-Star Rumble','RAW - Realms of Ancient War','Super Puzzle Fighter II Turbo HD Remix','Don King Presents Prizefighter','Prison Break: The Conspiracy','Spider-Man: Shattered Dimensions','Penny Arcade Adventures: Episode One','Penny Arcade Adventures: Episode Two','Project Gotham Racing 4','Viva Pinata: Party Animals','Ninety-Nine Nights II','Naruto Shippuden: Ultimate Ninja Storm 3 Full Burst','The Chronicles of Narnia: Prince Caspian','Need for Speed: Undercover','SpongeBob HeroPants','SpongeBob Squarepants: Plankton\'s Robotic Revenge','Leela','Diablo III: Reaper of Souls','Diablo III: Reaper of Souls','Destroy All Humans! Path of the Furon','Dead to Rights: Retribution','Brothers: a Tale of Two Sons','The Bureau: XCOM Declassified','Karaoke Revolution: American Idol Encore','Tony Hawk\'s Proving Ground','Disney Sing It HSM3','Sherlock Holmes vs Jack the Ripper','Samurai Shodown: Sen',"Up",'NPPL Championship Paintball 2009','NASCAR \'15','Naruto Shippuden: Ultimate Ninja Storm 3 Full Burst','Cabela\'s Dangerous Hunts 2009','Scott Pilgrim vs. The World','Scene It? Lights, Camera, Action','Scene It? Box Office Smash','Medal of Honor: Airborne','Monster Jam Path of Destruction','Orc Attack: Flatulent Rebellion','Lost Planet: Extreme Condition','FIFA 06 Road to FIFA World Cup','FIFA 06 Road to FIFA World Cup','FIFA 06 Road to FIFA World Cup','Yu-Gi-Oh! 5D\'s Decade Duels','Zumba Fitness: Join the Party','Pro Evolution Soccer 2007','Pro Evolution Soccer 2007','Voltron: Defender of the Universe','Viking: Battle for Asgard','Vancouver 2010 - The Official Video Game of the Olympic Winter Games','Universe at War: Earth Assault','UFC Personal Trainer: The Ultimate Fitness System','UFC Undisputed 2009','Devil May Cry HD Collection','Dragon Ball: Raging Blast','Dragon Ball: Raging Blast 2','Enslaved: Odyssey to the West','Far Cry Instincts: Predator','Harry Potter and the Half-Blood Prince','Harry Potter and the Order of the Phoenix','Ice Age: Dawn of the Dinosaurs','Ice Age: Continental Drift - Arctic Games','Chronicles of Riddick: Assault on Dark Athena','Sid Meier\'s Civilization Revolution','Tomb Raider: Anniversary','Poker Night 2','Street Fighter III: Third Strike Online Edition','Super Street Fighter II Turbo HD Remix','The Serious Sam Collection','SBK X: Superbike World Championship','SBK SuperBike World Championship','Kingdom Under Fire: Circle of Doom','Red Johnson\'s Chronicles - One Against All','Penny Arcade Adventures: Episode Two','Panzer General Allied Assault','Ninety-Nine Nights II','NCAA Basketball 09 March Madness Edition','MUD - FIM Motocross World Championship','MXGP - The Official Motocross Videogame','Mortal Kombat vs. DC Universe','Mortal Kombat Arcade Kollection','Lord of the Rings: Battle for Middle-Earth II','Fist of the North Star: Ken\'s Rage','Guncraft: Blocked and Loaded','Green Lantern: Rise of the Manhunters','Happy Tree Friends False Alarm','Tom Clancy\'s Ghost Recon: Future Soldier','Pinball Hall of Fame: The Williams Collection')

names(gameNameDict) = tolower(c('Modern Warfare 2','Modern Warfare 3','Modern Warfare','Battlefield: Bad Co. 2','COD: Black Ops II','DEAD RISING','Halo 3: ODST Campaign Edition','Halo: Combat Evolved', 'LOST PLANET 2','NFS ProStreet','Plants vs Zombies Garden Warfare','RESIDENT EVIL 5','World of Tanks: Xbox 360 Edition','TC\'s GRAW2','Assassin\'s Creed Revelations','Samurai Shodown SEN','[PROTOTYPE]','ACE COMBAT: AH','Battlefield: Bad Co.','DW6','DW6 Empires','DmC','PAC-MAN GHOSTLY ADV','PAC-MAN GHOSTLY ADV 2','Assassins Creed IV','ZOE HD','Dance Evolution / DanceMasters','Blackwater (video game)','Ace Combat 6','State of Decay (video game)','Army of TWO: TFD','Condemned','LUMINES LIVE! Standard Edition','Bangai-O HD','Banjo Kazooie: N and B','Blackwater Kinect','Cabela\'s African Adventure','Cabela\'s BGH 2010','Cabela\'s DH 2011','Cabela\'s NAA','Cabela\'s Survival: SoK','Viva Piata: TIP','Life Is Strange Episode 1','Marlow Briggs','Fable 2','Fable 3','Falling Skies','Feeding Frenzy 2','Full Game - Luxor 2','Oblivion','Skyrim','GTA V','GTA IV','Blacksite','BlazBlue','BLAZBLUE CS','Blazing Angels 2','Blazing Angels','2010 FIFA World Cup','3D Ultra Minigolf','Adventure Time: Explore','Alien Breed Episode 1','Avatar: TLA: TBE','Beijing 2008','Bejeweled 2','Ben 10 Ultimate Alien','Beowulf','Bomberman Live','Brütal Legend','Bully Scholarship Ed.','Cabela\'s North American Adventure','AA: True Soldiers','Brothers in Arms: HH','Castlevania: LoS 2','Castlevania LoS','Castlevania HD','Cloudy with a...','TC\'s RainbowSix Vegas','TC\'s RainbowSix Vegas2','James Cameron\'s Avatar: The Game','Cameron\'s Avatar','Transformers: WFC','ATARI ASTEROIDS/ASTEROIDS DELUXE','Viva Pinata: TIP','TC\'s SC Double Agent','AC for Answer','E4','Geometry Wars Evolved','Hydro Thunder','Kameo','Monkey Island 2: Special Edition','The Secret of Monkey Island: S.E.','OF: Dragon Rising','WATCHMEN','WATCHMEN PART 2','Phantom Breaker:Battle Grounds -Cocoa\'s Nightmare Attack-', 'Zone of the Elders HD', 'Puzzle Quest','Runner2','Sam & Max Beyond Time and Space','SONIC 4 Episode II','SOULCALIBURII HD ONLINE','JASF','Tour de France 2009 - The Official Game','Tour de France 2013 - 100th Edition',"50 Cent: BotS",'Disney/Pixar: Brave The Video Game','Battlefield 2: MC','DBZ for Kinect','DBZ: BURST LIMIT','LEGO Indiana Jones 2','LEGO Indiana Jones','LEGO Star Wars III','LEGO Lord of the Rings','LEGO Harry Potter','TWD: Survival Instinct','Transformers 2','The Warriors: SB','The Raven Episode 1','METAL GEAR SOLID 2 AND 3 HD','Metal Gear Solid 2 & 3: HD Edition','The Witcher 2: Assassins of Kings Enhanced Edition','American Wasteland', 'A.R.E.S.','Sonic\'s UGC','SplinterCellConviction','WSOP: TOC','WtWTA','Titanfall Deluxe Edition','Worms The Revolution Collection','Valiant Hearts','Young Justice','Zeno Clash UE','Thrillville: OTR','The Price Is Right','The Penguins of Madagascar','THE KING OF FIGHTERS 2002 UNLIMITED MATCH','TCs H.A.W.X','SWTCW: Republic Heroes','Superstars V8 NC','Street Fighter II\' HF','DOA5 Last Round','Super Hero Squad: TIG','5th Grader','5th Grader: Game Time','Alaskan Adventures','Alaskan Storm','Hannah The Movie','ASSAULTHORIZON','Assault on Dark Athena','Naval Assault','Cabela\'s Safari','Chivalry','CW: Secret Missions','COD: Black Ops III','Cabela\'s Hunting Party','C&C3: Kane\'s Wrath','C&C Red Alert 3','Commanders: Attack','Blackgate Deluxe Ed.','0D Beat Drop','Bakugan: DOTC','Bakugan','DDR/DS Universe','DDR/DS Universe 2','DDR Universe 3','At Worlds End','UEFA CL 2006-2007','Zeit²','WWE Legends','Quantum of Solace','Teenage Mutant Ninja Turtles','The Strike','DW: Strikeforce','Strania','STORM Generations','BLADESTORM','NARUTO STORM 3','Naruto: Ninja Storm 2','Naruto Shippuden: Ultimate Ninja Storm 3','NINJA STORM 2','Naruto Shippuden: Ultimate Ninja Storm 3','SpongeBob: Truth-Sq.','SpongeBob UnderPants!','SpongeBob SquarePants: Underpants Slam!','Star Ocean: TLH','HSM3 Senior Year DANCE','Deathspank T.O.V.','Digimon: ASR','RAW','Puzzle Fighter HD','Prizefighter','Prison Break','Spider-Man:Dimensions','Penny Arcade Episode 1','Penny Arcade Episode Two','PGR 4','Party Animals','N3II: Ninety-Nine Nights','Naruto Shippuden: Ultimate Ninja Storm 3','Narnia: Prince Caspian','NFS Undercover','SBHP','SB: Robotic Revenge','Deepak Chopras Leela','Diablo III: Reaper of Souls – Ultimate Evil Edition','Diablo III','DAH! Path of the Furon','DTR: Retribution','Brothers','The Bureau','Karaoke Revolution Presents: American Idol Encore','TH Proving Ground','High School Musical 3: Senior Year Dance','Sherlock Holmes','SAMURAI SHOWDOWN SEN','DisneyPixar UP','Paintball 2009','NASCAR \'15 Victory Edition','Naruto Shippuden: Ultimate Ninja Storm 3','Dangerous Hunts 2009','SCOTT PILGRIM THE GAME','Scene It? LCA','Scene It? BOS!','MOH Airborne','MJ Path of Destruction','Orc Attack','Lost Planet','2006 FIFA World Cup','FIFA 06 RTFWC','FIFA 06: Road to FIFA World Cup','Yu-Gi-Oh! 5D\'s Decade Duels Plus','Zumba Fitness','Winning Eleven: Pro Evolution Soccer 2007','Winning Eleven 2007','Voltron','Viking: Battle for Asgard','Vancouver 2010','Universe at War','UFC Personal Trainer','UFC 2009 Undisputed','DMC HD Collection','DB: Raging Blast','DB: Raging Blast 2','Enslaved','FC Instincts Predator','Harry Potter HBP','Harry Potter OOTP','Ice Age 3','Ice Age 4','Assault on Dark Athena','Civilization Revolution','Lara Croft Tomb Raider Anniversary','Telltale Games\' Poker Night 2','Street Fighter III: Online Edition','Super Street Fighter 2 Turbo HD','Serious Sam','SBK X','SBK','KUF: Circle of Doom','Red Johnson\'s Chronicles','Penny Arcade Episode 2','Panzer General','Ninety-Nine NightsⅡ/NA','NCAA Basketball March Madness Edition','MUD','MXGP','Mortal Kombat vs. DCU','Mortal Kombat Arcade','Lord of the Rings, BFME II','Fist of the North Star','Guncraft','Green Lantern','Happy Tree Friends','Ghost Recon: Future Soldier','Pinball Hall of Fame'))
```

#### Wrangling Publishers
Microsoft has said they need publishers and rights owners to agree to make their games backwards Compatible, so it is important to ensure publishers are up to date. Publishers also are often written inconsitently, but luckily many use unique names that make it easy like "Konami" or "Namco Bandai." This was mostly a matter of finding matching words in the name to update them to be consistent. However, it has been a decade since some of these games have been released and some publishers have fallen and others have risen from their ashes while others have been acquired. I researched and updated publishers and game to be closer to their modern day states as Microsoft would need to do. This is difficult and while I tried, there are probably a few mistakes or polishing that could still be done. 

## Missing values
As I got toward the end of polishing, I realized I was still missing quite a few data points. I went back through my scrapers and grabbed even more information so I could fill in missing spots. However, that still left many missing values. There are 4132 missing values out of 66312 values (6.2%) though some are less important than others because I found later that I would not be using them (developers). This results in missingness, which occurs when at least some of an observation’s values are not present within the dataset. This messes up statistical and machine techniques because the algorithm won't understand how to crunch the numbers. Deleting games with a missing value doesn't help because it severely limits the amount of data we have for analysis. In this problem, we would lose 1343 out of 1842 lines of data. 

#### Imputation
To solve this, we use imputation to fill in the missing values. The data is missing at random because certain points are missing from the dataset for because games weren't reviewed, submitted to uservoice or the point was simply missing. There isn't a  pattern to fill in the continuous or categorical values and it would be inappropriate to assign an average score where review scores are missing. I used K nearest neeighbors (KNN) to fill in missing values. KNN makes a tesselated boundary using existing data points and judges where in the boundary the missing point is. The formula uses the K closest observations to the missing data point, and predicts the majority class as the outcome. I used the square root of the number of rows (42.9) as a K value to strike a balance of being robust to outliers, stable boundaries and local variations.

## Prediction
I started by making a subset of the data that was either never going to be backwards Compatible or already was made backwards Compatible so this could be a supervised learning problem. I used multiple linear regression using the relations between the portion of the data that says whether or not a game is backwards Compatible. I needed to use a generalized linear model in this regression because I am using both categorical and continuous data to predict the categorical outcome of whether a game would be made backwards Compatible.I used forward AIC on a scope from an empty dataset except the game name and a few irrelavent predictors  up to all the data minus those useless predictors sto find the most effective combination of the data which finds the most significant predictors for analysis. 

Once I had a model, I applied it to the entire dataset. Unfortunately, the truth table marked a much larger value of the true backwards Compatible values as false.

#### Influence Plot
![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png)

```
                                  StudRes         Hat        CookD
Block Party                 -0.0003172085 0.603966224 7.413479e-05
Mass Effect 3                3.7113489887 0.003261879 2.748762e-01
Minecraft: Xbox 360 Edition -3.4701885531 0.214247150 6.089149e-01
```

#### Variance Factors

```
                          GVIF Df GVIF^(1/(2*Df))
gamesOnDemandorArcade 1.920969  2        1.177281
price                 1.323202  1        1.150305
reviewScorePro        1.495966  1        1.223097
isOnXboxOne           1.102592  1        1.050044
isMetacritic          1.083634  1        1.040977
isKinectRequired      1.000000  1        1.000000
isConsoleExclusive    1.070122  1        1.034467
xbox360Rating         1.714716  1        1.309472
hasDemoAvailable      1.397634  1        1.182216
isListedOnMSSite      1.725614  1        1.313626
votes                 1.684826  1        1.298008
numberOfReviews       1.942460  1        1.393722
DLgameAddons          1.131993  1        1.063951
DLavatarItems         1.449349  1        1.203889
ESRBRating            1.382044  4        1.041275
```
Variance factors are slightly more than 1 which proves the predictors are only slightly correlated but not enough to worry about colinearity.

#### Coefficients

```
                         (Intercept) gamesOnDemandorArcadeGames on Demand 
                        1.074016e-03                         2.975601e+00 
    gamesOnDemandorArcadeRetail Only                                price 
                        1.904019e-01                         9.301107e-01 
                      reviewScorePro                      isOnXboxOneTRUE 
                        1.030424e+00                         2.391521e-01 
                    isMetacriticTRUE                 isKinectRequiredTRUE 
                        2.288119e+00                         2.629131e-07 
              isConsoleExclusiveTRUE                        xbox360Rating 
                        4.156885e+00                         2.691192e+00 
                hasDemoAvailableTRUE                 isListedOnMSSiteTRUE 
                        1.882399e+00                         2.194432e-01 
                               votes                      numberOfReviews 
                        9.999572e-01                         1.000005e+00 
                        DLgameAddons                        DLavatarItems 
                        9.677326e-01                         1.021798e+00 
      ESRBRatingeC (Early Childhood)               ESRBRatingE (Everyone) 
                        5.869419e-06                         1.532840e+00 
                ESRBRatingM (Mature)                   ESRBRatingT (Teen) 
                        8.131176e-01                         1.039226e+00 
```

#### McFadden's Pseudo R^2 Value
28.4% of the variability in the backwards Compatibility variable appears to be explained by the predictors in the model.

<!-- #### Confidence Intervals -->
<!-- ```{r eval=TRUE, tidy=TRUE, echo=FALSE, comment=NA, warning=FALSE}  -->
<!-- confint(glogit.optimizedFoAIC) -->
<!-- ``` -->

#### Model Summary

```

Call:
glm(formula = isBCCompatible ~ gamesOnDemandorArcade + price + 
    reviewScorePro + isOnXboxOne + isMetacritic + isKinectRequired + 
    isConsoleExclusive + xbox360Rating + hasDemoAvailable + isListedOnMSSite + 
    votes + numberOfReviews + DLgameAddons + DLavatarItems + 
    ESRBRating, family = "binomial", data = dataUltKNN)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-2.4931  -0.5362  -0.2293  -0.0893   3.5026  

Coefficients:
                                       Estimate Std. Error z value
(Intercept)                          -6.836e+00  1.227e+00  -5.573
gamesOnDemandorArcadeGames on Demand  1.090e+00  7.643e-01   1.427
gamesOnDemandorArcadeRetail Only     -1.659e+00  8.270e-01  -2.006
price                                -7.245e-02  1.316e-02  -5.505
reviewScorePro                        2.997e-02  7.627e-03   3.930
isOnXboxOneTRUE                      -1.431e+00  3.808e-01  -3.757
isMetacriticTRUE                      8.277e-01  2.776e-01   2.981
isKinectRequiredTRUE                 -1.515e+01  3.697e+02  -0.041
isConsoleExclusiveTRUE                1.425e+00  5.232e-01   2.723
xbox360Rating                         9.900e-01  2.351e-01   4.212
hasDemoAvailableTRUE                  6.325e-01  1.997e-01   3.167
isListedOnMSSiteTRUE                 -1.517e+00  5.724e-01  -2.650
votes                                -4.280e-05  1.467e-05  -2.916
numberOfReviews                       4.746e-06  1.792e-06   2.649
DLgameAddons                         -3.280e-02  1.351e-02  -2.428
DLavatarItems                         2.156e-02  1.030e-02   2.094
ESRBRatingeC (Early Childhood)       -1.205e+01  2.790e+03  -0.004
ESRBRatingE (Everyone)                4.271e-01  2.240e-01   1.907
ESRBRatingM (Mature)                 -2.069e-01  2.624e-01  -0.788
ESRBRatingT (Teen)                    3.848e-02  2.307e-01   0.167
                                     Pr(>|z|)    
(Intercept)                          2.50e-08 ***
gamesOnDemandorArcadeGames on Demand 0.153678    
gamesOnDemandorArcadeRetail Only     0.044895 *  
price                                3.70e-08 ***
reviewScorePro                       8.51e-05 ***
isOnXboxOneTRUE                      0.000172 ***
isMetacriticTRUE                     0.002869 ** 
isKinectRequiredTRUE                 0.967311    
isConsoleExclusiveTRUE               0.006470 ** 
xbox360Rating                        2.53e-05 ***
hasDemoAvailableTRUE                 0.001539 ** 
isListedOnMSSiteTRUE                 0.008060 ** 
votes                                0.003541 ** 
numberOfReviews                      0.008063 ** 
DLgameAddons                         0.015165 *  
DLavatarItems                        0.036229 *  
ESRBRatingeC (Early Childhood)       0.996555    
ESRBRatingE (Everyone)               0.056511 .  
ESRBRatingM (Mature)                 0.430536    
ESRBRatingT (Teen)                   0.867538    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 1593.9  on 1841  degrees of freedom
Residual deviance: 1141.8  on 1822  degrees of freedom
AIC: 1181.8

Number of Fisher Scoring iterations: 16
```

#### Truth Table

```
       prediction
truth      0    1
  FALSE 1521   34
  TRUE   208   79
```


## Conclusions
Metacritic, having a version on Xbox One, a lower price, higher user reviews on Xbox's website, and having a demo are the most significant predictors with Uservoice votes lagging slightly behind. There was also moderate help by having a newer release date, more avatar items, less game add ons and more reviews. Retail only games are also negatively related and has a somewhat significant relation. Also, having a Kinect requirement or required peripherals brought the probablity to zero.

This makes me wonder if it is easier to test and approve games by usiner their demos. I am also glad that Uservoice votes positively significantly influenced their priorites. I am also not suprised they try to get higher reviewed games approved first. I imagine having a lower price and being available on their site makes them want to prioritize games people are likeliest to buy digitally on impulse.

## Future Work
* Implementing a method of machine learning called clustering to examine the data
* Implementing a neural network if given the time
* Automatically perform update scrape data and machine learning predictions
* Scraping sales to give consumers advice on which Xbox 360 games to buy that week
* Game covers are scraped but not implemented, which I feel would make the website prettier