# Roi Bachynskyi

## 23 June 2025
- Made a walkthrough of Tour of Rust and refreshed what I learnt during the labs (for, if, match, Option, Result, etc.)
- Read more Ownership and Borrowing which I didn't understand during the labs
- Explored Smart Pointers
- Explored the difference between ```&str``` and ```String```

## 24 June 2025
- Completed the MA Lab 0 to solidify knowledge gained yesterday
- Flashed STM32
- Completed tasks from MA Labs 2 and 4 (only LEDs and a button)


## 25 June 2025
- Completed the MA Lab 3 to check PWM and ADC on STM32
- Completed the Morse code exercise from MA Lab 2 which I missed and spent 1 hour on it (no_std environment demands another approach to Strings)
- Started the MA Lab 5 and started struggling with SPI, continue tommorow

## 26 June 2025
- Completed MA Lab 5 and MA Lab 6, SPI and I2C were revisited
- Reached 45/94 exercise from Rustlings

## 27 June 2025
- Reached 69/94 exercise from Rustlings
- Started to learn how to use Helix Editor
- Started organizing the workspace

## 30 June 2025
- Completed Rustlings 
![Rustlings Fe-nish](https://github.com/user-attachments/assets/0c2fa26a-1e5a-485a-a8a6-1218551ee443)

- Moved to the lab computer and installed Ubuntu
- Installed i3 window manager to start learnign no-mouse way of using the computer and OS
- Chose my [first issue](https://github.com/orgs/WyliodrinEmbeddedIoT/projects/1?pane=issue&itemId=116432064&issue=WyliodrinEmbeddedIoT%7Cembassy%7C4)
- Flashed the NXP board

## 1 July 2025
- Tried to flash the examples for the NXP Board: `blinky_nop` worked as intended, `button_executor` didn't print anything
- Spent 1 hour trying to understand what is wrong in the second example
- Explored `pint.rs` and `gpio.rs` files
- Thanks to George, the problem was found: allegedly, the asyncronous program didn't work because of the secure and non-secure mode. The only thing that could fix it is 1) flashing 2) `probe-rs attach` 3) pressing the reset button
- Explored the `fmt` module in `embassy-rp` which was used in `embassy-nxp`
- Not finished writing the code for features because of feature propagation 

## 2 July 2025
- Read a half of the book about macros ([link](https://lukaswirth.dev/tlborm/introduction.html)) to understand what happens in `fmt.rs`
- Thanks to George, understood the logic behind `defmt` and `log`
- Implemented `log-to-defmt` feature
- Closed my first issue

## 3 July 2025
- Chose my new [issue](https://github.com/WyliodrinEmbeddedIoT/embassy/issues/3) 
- Read a chapter about USART to understand how it works on this board
- Made some changes in the pull request as George asked me
- Started reading `embassy-rp` implementation of UART

## 4 July 2025
- Found a [pinout](https://mcuxpresso.nxp.com/en/pins) for LPC55S69 board (the datasheet is indeed awful)
- Wrote down pins for UART for each Flexcomm
- Found an instruction how to work with USART on LPC55S69 (planning to work on the implementation during the weekend)
- Explored `lpc55-pac`  

## 5-17 July 2025
- On vacation with my family

## 18 July 2025
- Fixed last problems in my ```log-to-defmt``` issue, rebased the changes from ```embassy``` GitHub repository and started my first [PR](https://github.com/embassy-rs/embassy/pull/4416)
- Started to implement USART following the documentation advice (got a bit complicated)

## 21 July 2025
- Dirbaio asked to remake the commit
- The initial guess was the correct one, therefore I read `fmt.rs` in `embassy-rp` several times once again, I added one more macro (`unimplemented!()`) that wasn't introduced in `embassy-rp`
- Dirbaio approved my PR, yay!

## 22 July 2025
- Started to work on USART once again
- Implemented writing to TX and reading from RX in loopback mode
- Tried to figure out why it didn't work physically, but failed, continue tomorrow 

## 23 July 2025
- Tried to debug my USART code and see what is wrong
- Currently, it's failing to work in normal mode (instead of loopback mode), something is blocking the signal
- Tried to debug it using Arduino Uno as a slave, but it didn't work out. Then, I thought the problem was in USART connection with the computer, but sadly, not.
- Denis told me to look at `lpc-hal`, George suggesed looking at C-implementation
- Helped Irina with her `nxp-pac` and `chiptool` (unfortunately, it doesn't have any documentation)

## 24 July 2025
- Finally, I fixed my USART so that I can transmit bits using wires, not loopback mode. Thanks to Alex!
- The problem was in `IOCON` register, it lacked full configuration
- Left earlier because I had an appointment to a dentist

## 25 July 2025
- Read `embassy-rp`'s implementation of USART to understand the ownership rules and how to write driver implementation
- Tried to help Denis with his SPI, but there's still something we both don't see
- For the whole period I had problems with flashing of the NXP board: every time I restarted it, `probe-rs` didn't flash the board. Them problem was fixed by someone, but the change has not been used by the main branch
- Implemented baudrate configuration for USART

## 28 July 2025
- Wrote all the blocking functions for USART, so that an array of bytes can be either read or written.
- Had problems with reading, but it was so simple: `fifordnopop` should have been used to check the FIFO state instead of `fiford`, every reading of `fiford` register entails data loss

## 29 July 2025
- George gave me two ways of further implementation: either polishing the code out and writing the driver itself or writing the interrupt-driven version of USART
- Started to polish the code, I was thinking a lot about how to connect USART and Flexcomm so that USART owns it
- Came up with a very simple idea: just own the pins like `embassy` does so that it is prohibited to use the same Flexcomm twice.

## 30 July 2025
- Started to write the driver outline from scratch once again
- I started with `impl_instance!()` to ensure that every USART has appropriate registers and can be configured,but it was a bit hard to work with `macro_rules`, but George helped me
- Wrote some constructors and main functions for USART TX and USART RX

## 31 July 2025
- I tried to write the code so that USART struct is as generic as possible
- However, because of Rust safety rules, it cannot deduce the used PAC type in USART struct making using of registers there impossible
- That's why I moved all the logic into the generic `Instance` (I feel like when I make a PR, I'll have many comments, though)
- Almost finished the implementation of blocking USART (hopefully)

## 1 August 2025
- Finished blocking USART implementation, introduced `usart_blocking.rs` as an example
- Talked with George about further implementation of asynchronous mode with interrupts
- Started my second [pull request](https://github.com/embassy-rs/embassy/pull/4497)
- I spent much time to implement it, but there are still a lot of things to do. However, I like it :)

## 4 August 2025
- Had 24 comments from George and around 7 comments from the American
- Resolved all their observations
- Learnt what is `critical_section`
- A strange bug happened to me yesterday: while working on the improvements, Denis asked me to help him with blocked device. I ran `cargo run` with blinky example, then used LinkFlasher, restarted my PC, and all the contents of a folder where `embassy` fork was disappeared. Unfortunately, I didn't save my code, so I wrote what I remembered once again.
> Do more backups.
- Helped Irina with synchronizing her fork with the upstream and learnt how to do it using `git` in terminal

## 5 August 2025
- Started to explore interrupts and the logic behind them in USART
- Started to read about DMA, but George advised starting with usage of interrupts 
- Spent a lot of time to understand how to harness the interrupts 


## 6 August 2025
- Continued to 'fight' interrupts, they're not as intuitive as I thought, and they need to be harnessed. For some reason, they fired every time I unmasked them. I guess it's because of defmt lagging
- Fixed a bug in my PR with `set_baudrate` function, now it doesn't panic
- Learnt what is the ring buffer
- Started to read `buffered.rs` in `embassy-rp` and I'm going to use it as a skeleton for my code.

## 7 August 2025
- Went to the family doctor to get a paper for the IP Workshop

## 8 August 2025
- Arrived in Târgu Mureș to help as a volunteer

## 9 August 2025
- Andrei Dragota and I printed a lot of posters and pasted them on the walls
- Carried some chairs, desks, other stuff at the university 

## 10 August 2025
- New posters were pasted :)
- Helped Laur with cabling and routers

## 11 August 2025
- Enroled in Autonomous cars course sponsored by NXP
- Assembled a car and made a logo for our team in Fusion

## 12 August 2025
- Learnt how to solder, I really loved doing this, it was like a meditation for me
- Finished my [second PR](https://github.com/embassy-rs/embassy/pull/4497) (it's the part of the [issue](https://github.com/orgs/WyliodrinEmbeddedIoT/projects/1/views/1?pane=issue&itemId=116432065&issue=WyliodrinEmbeddedIoT%7Cembassy%7C3)) 

## 13 August 2025
- My [PR](https://github.com/embassy-rs/embassy/pull/4497) was merged at last. While I had nothing to do in my free time, I decided to start Philipp Oppermann's guide called "[Writing an OS in Rust](https://os.phil-opp.com/)"
- Completed the [first post](https://os.phil-opp.com/freestanding-rust-binary/) of OS Guide
- Started exploring code for the car. Teo uploaded the code on GitHub.
- Started to explain my junior colleague how processor works, how `async` works, even opened [lecture notes](pmrust.pages.upb.ro)

## 14 August 2025
- Started reading Andrew Tannenbaum's book "Modern Operating Systems"
- Completed the [second post](https://os.phil-opp.com/minimal-rust-kernel/) of the OS Guide

## 15 August 2025
- Worked on code for the car
- Went insane because it was so slow and it didn't follow the line while everyone around made it

## 16 August 2025
- Our car was following lines, yay
- But I wanted to make it smoother and faster at the same time
- I started experimenting, but because of this wheels of the car were flying :), my colleagues didn't appreciate this
- I even created issues, but I overcomplicated things

## 17 August 2025
- Made two significant changes: one, replaced slope `m` with `atan2`, two, I used avarage instead of summation
- It was a miracle for me, the car went so perfectly

## 18 August 2025
- There were some problems with a camera, the code didn't work, I started to debug it, but in reality, the problem was in SCL and SDA: they were touching each other. We killed a camera :)
- I started experimenting again: playing with speeds of outer and inner wheels to make the car faster on the corners, decreasing the whole speed before the turn
- But the car didn't work well, and my teammate made a very wise decision that made a huge impact: I wasn't allowed to to make any experiments, because our car as already OK (I didn't see that, I was winding, the reason will be described later).
- I was mad because our rival was faster, but I refrained and kind of gave up because I knew that if I do new things and break what existed, I won't be able to fix it even having [version control](https://github.com/frihetselsker/ipw-autonomous-cars)
- Started the [third post](https://os.phil-opp.com/vga-text-mode/) of the OS guide

## 19 August 2025
- Completed the [third post](https://os.phil-opp.com/vga-text-mode/) of the OS guide
- Tested the car several times, then we changed the location where the race was supposed to be
- The car got blind, but we fixed it
- Junior colleagues fixed the stuck servo motor and our car was perfect
- We won the first place at NXP Cup-like event, yay :) 

## 20 August 2025
- Read the [fourth post](https://os.phil-opp.com/testing/) while coming home

## 21 August 2025
- I took a day-off, I needed some sleep
- Started implementing the function from the [post](https://os.phil-opp.com/testing/), but for some reason, QEMU is not opening when calling `cargo test`
- I'm planning to open an issue and ask Andrei Dragota about it 

## 22 August 2025
> TXLVL interrupt fires whenever the number of bytes in the TX FIFO is **less or equal** the threshold programmed in FIFOTRIG.TXLVL.
> RXLVL interrupt fires whenever the number of bytes in RX FIFO is **more or equal** the threshold programmed in FIFOTRIG.RXLVL.
- Found this key thing in implementing an asynchronous mode for USART
- Started to read about DMA and its usage in USART
- Opened an [issue](https://github.com/phil-opp/blog_os/issues/1433) on the GitHub repository of the OS guide

## 25 August 2025
- Attented the 1st day of 'FutureShapers' summer school organized by Politehnica, Honeywell, INSA and EELISA
- Started resarching about capabilities of asyncronous programming on Arduino Uno
- In fact, this board can handle this and there is a library called [AceRoutine](https://github.com/bxparks/AceRoutine)
- I really admired cooperative tasks on PM and decided to use this Embassy-like library

## 26 August 2025
- Philipp Oppermann answered to my question and resolved my issue.
- I worked on the sensors for the summer school and eventually connected two boards together using `SoftwareSerial`
- Learnt how to organize code on Arduino projects using headers (`.h`) and source files (`.cpp`)

## 27 August 2025
- Wrote asynchronous code for all the sensors, communication and the menu
- However, it is easier to write code for Embassy devices that to Arduino

## 28 August 2025
- Got a message from Irina that we were moving to `nxp-pac`
- Our team failed the presentation and didn't get the podium place.
- The communication channels collapsed and our plan was not implemented

## 29 August 2025
- Back to work
- Learnt differences between PACs made using `svd2rust` and `chiptool`
- Made some fixes at `Cargo.toml` for `embassy-nxp`
- Started rewriting GPIO section
  
## 30 August 2025
- Continued working on GPIO, I worked a little bit
  
## 31 August 2025
- Fixed GPIO code
- Almost fixed PINT code, still don't know how to rewrite interrupts in this case
- Opened a [PR](https://github.com/i509VCB/nxp-pac/pull/10) to `nxp-pac`, enums should be merged
- Learnt how to use `git branch` and `git checkout`. Just read the documentation with a simple command like `man git-branch` 
  
## 1 September 2025
- Finally fixed PINT code
- Figured out how to rewrite USART code better
- Figured out how to fix the problem with a linker together with Irina
- However, now the board faults, and we have no idea why. I'll ask George once he comes
- Learnt how to use `git reset`

## 2 September 2025
- Adrian helped me with debugging. The problem was really simple: instead of modifying `SYSCON` I was writing to it nullifying other fields that shouldn't be zero
- Started rewriting my USART code, there was such a mess there
- I had an idea: what if I use two macros that will return the code based on the USART register that I can store in the `Info` struct for pin and clock configuration?
- For this, I merged again some fields/registers in `nxp-pac` for Flexcomms. Also, i509VCB told to merge all the `piox_y` registers, it has also reduced the code size in GPIO
- My idea for USART was really bad, I had a lot of repeating code which is unaccepted in terms of embedded development. I should ask George about it, because now I have no ideas.

## 3 September 2025
- Thanks to George, I came up with new ideas. New merges of fields/registers in `nxp-pac` and some helping functions for choosing the correct configuration in `FLEXCOMMx` and `SYSCON`
- Finally rewrote USART code, now it's much better than before. Still have a visibility issue from `Info` struct: It's more private than the trait `SealedInstance`

## 4 September 2025
- Yay, I finished it. However, FLEXCOMM3 still doesn't work, there is fishy with pins, because loopback mode does work. George also has no idea why.
- Another attempt to start wrtiting asynchronous code for USART, but before doing this, I need to implement DMA controller, it should be much better than simple interrupts
- Also, it will save time to others who will implement async code for their peripherals
