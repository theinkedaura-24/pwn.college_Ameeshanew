# Shinsu DEXquest
This challenge requires reverse engineering an Android application (.apk file). The goal is to bypass a crashing mechanism and uncover a hidden flag by analyzing the application's decompiled source code and reversing a complex input validation algorithm.

## My solve
**Flag:** `citadel{f33ls_g00d_to_n0t_g3t_d33p_fr13d}`

First, I installed and ran the .apk file on an Android emulator. The app presents a main screen with a door, and you can swipe to two other rooms.

One room has a button labeled "Enter a barrier". Tapping it immediately crashes the app with a toast message: "You were absolutely deep fried by the barrier".

The other room has a button labeled "Observe the Observer". This leads to a screen with a number input field. Entering any number and submitting it would return different, seemingly random results.

The crash indicated that a prerequisite condition was not being met, and the random outputs suggested the validation logic was intentionally obfuscated. It was time to look at the code.

As the challenge name "DEXquest" suggests, the core of the challenge lies in the DEX (Dalvik Executable) files. I used JADX to decompile the .apk and analyze its source code.

I started at the entry point, MainActivity.java, which quickly redirects to the Room.java class.

Inside Room.java, I found a key switch statement that controls navigation between different screens (Fragments) of the app: NextRoom, Barrier, Entrance, Observer, and Vault. The Vault screen was not accessible through the UI, which made it my primary target. The crash was happening when trying to access NextRoom.

In the NextRoom.java code, I found a getFlag() function. However, its logic depended on a password obtained from the Vault class: byte[] password = new BigInteger(Vault.INSTANCE.getPassword()).toByteArray();. This confirmed that I needed to solve the Vault's puzzle first.

Finally, I analyzed Vault.java. It contained a very long and complex function named correctDigits(). This function takes the user's number input and checks it digit-by-digit against a series of specific mathematical conditions. If any digit is wrong, it returns a random value, explaining the behavior I saw during dynamic analysis.

The only way forward was to reverse-engineer the logic within correctDigits(). I carefully translated the validation checks from the decompiled Java/Kotlin code into a simple script. By satisfying each condition sequentially, I could generate the one valid 99-digit number.

The correct 99-digit input is:
246451121036528202623890120011222498714684815355211850969748918921930947358058258074789422723982625

After generating the number, I went back to the running app:

Navigated to the "Observe the Observer" screen.

Pasted the 99-digit number into the input field.

Upon submission, a toast message appeared: "You have acquired a brand new Shinsu Stabilizer Crystal!". This meant the condition to bypass the barrier was now met.

I went back and tapped the "Enter a barrier" button. This time, instead of crashing, it successfully loaded the NextRoom screen.

This screen had a single button labeled <flag>, which, when pressed, copied the flag to my device's clipboard.

## What I learned
This challenge was a fantastic exercise in Android reverse engineering. 
I learned how to approach a black-box Android app, starting with dynamic analysis to understand its behavior and identify points of interest (like crashes or strange outputs). 
The main takeaway was the process of diving into decompiled code with JADX, tracing logic across multiple classes, and patiently reverse-engineering a complex validation algorithm to generate the required input. 
It highlighted the importance of static analysis when dynamic analysis hits a wall.

## References 
I used the following primary tool used for decompiling the APK and analyzing its Java source code.

https://github.com/skylot/jadx
