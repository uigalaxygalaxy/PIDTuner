<script lang="ts">
import Header from '$lib/Header.svelte';
import Choice from '$lib/Choice.svelte';

let technique = $state('None');
let guess = $state(0);
let kD = $state(0);
let iteration = $state(0);
let kDMath = $state([0]);
let kPMath = $state([0]);
let delta = $state(0);
let kDelta = $state(0);
let techniqueStarted = $state(false);
let firstNo = $state(true);
let firstYes = $state(true);
let action = $state('oscillate');
let flavor = $state('Start with finding a rough estimate for kP. As long as it is high enough that it will slow down fast without oscillating will be enough for our purposes.');
let lastButton = $state('Find kD');

let showFinalResults = $state(false);
let warning = $state('');


function pickTechnique(choice: string) {
    technique = choice;
}

function startTechnique() {
    techniqueStarted = true;
    if(technique === 'kP First') {
        kDelta = kD;
        kDMath = [0];
        kPMath = [guess];
        delta = kPMath[0];
    }
    if(technique === 'kD First') {
        action = 'oscillate';
        kDelta = kD;
        kDMath = [kD];
        kPMath = [guess];
        delta = kPMath[0];
    }
    if(technique === 'Oscillations') {
        action = 'oscillate above a set amount of times';
        flavor = 'Find the highest kP value you can while staying below a set amount of oscillations.'
        kDMath = [0];
        kDelta = kD;
        kPMath = [guess];
        delta = kPMath[0];
        
    }
}

    function Yes() {
    if (firstNo) {
        firstNo = false;
    }
        if (technique === 'kP First') {
            kPMath.push(kPMath[iteration]).toFixed(3);
            kDMath.push(kDelta + kDMath[iteration]).toFixed(3);
            iteration++;
        }
        if(technique === 'kD First') {
            if(finalButtonPresses === 0) {
            delta = delta / 2;
            kPMath.push(kPMath[iteration] - delta).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
        }
                    if(finalButtonPresses === 1) {
            kDelta = kDelta / 2;
            kPMath.push(kPMath[iteration]).toFixed(3);
            kDMath.push(kDMath[iteration] - kDelta).toFixed(3);
            iteration++;
        }

                            if(finalButtonPresses === 2) {
                                if(!firstYes) {
        delta = delta / 2;
        }
            kPMath.push(kPMath[iteration] - delta).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
        }
        
        }
        if(technique === 'Oscillations') {
            if(finalButtonPresses === 0) {
            delta = delta / 2;
            kPMath.push(kPMath[iteration] - delta).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
            }
            if(finalButtonPresses === 1) {
        if(!firstYes) {
            kDelta = kDelta / 2;
        }
            kPMath.push(kPMath[iteration]).toFixed(3);
            kDMath.push(kDMath[iteration] + kDelta).toFixed(3);
            iteration++;

        }
    }

}

function No() {
        if (firstYes) {
        firstYes = false;
    }
    if (technique === 'kP First') {
            kPMath.push(delta + kPMath[iteration]).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;

        }
    if (technique === 'kD First') {

                    if(finalButtonPresses === 0) {
                                if(!firstNo) {
            delta = delta / 2;
        }
            kPMath.push(kPMath[iteration] + delta).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
        }
                            if(finalButtonPresses === 1) {
                                if(!firstNo) {
        kDelta = kDelta / 2;
        }
            kPMath.push(kPMath[iteration]).toFixed(3);
            kDMath.push(kDMath[iteration] + kDelta).toFixed(3);
            iteration++;
        }
                                    if(finalButtonPresses === 2) {
                                if(!firstNo) {
        delta = delta / 2;
        }
            kPMath.push(kPMath[iteration] + delta).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
        }
    }
    if(technique === 'Oscillations') {
                    if(finalButtonPresses === 0) {

                                        if(!firstNo) {
            delta = delta / 2;
        }
            kPMath.push(kPMath[iteration] + delta).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
    }
    if(finalButtonPresses === 1) {
            kDelta = kDelta / 2;

            kPMath.push(kPMath[iteration]).toFixed(3);
            kDMath.push(kDMath[iteration] - kDelta).toFixed(3);
            iteration++;
    }
}
}

function Unstable() {
    if (technique === 'kP First') {
        delta = delta / 2;
        kDelta = kDelta / 2;
            kPMath.push(kPMath[iteration] - delta).toFixed(3);
            kDMath.push(kDMath[iteration] - kDelta).toFixed(3);
            iteration++;
            delta = delta / 2;


        }
    if (technique === 'kD First') {
                                    if(finalButtonPresses === 0) {

        kDelta = kDelta * 0.8;
            kPMath.push(kPMath[iteration]).toFixed(3);
            kDMath.push(kDMath[iteration] * 0.8).toFixed(3);
            iteration++;
                                    }
    if(finalButtonPresses === 1) {
                delta = delta * 0.9;
            kPMath.push(kPMath[iteration] * 0.9).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
    }
        if(finalButtonPresses === 2) { 
warning = 'Your robot should not be unstable in this stage! I have not changed any values for you. Lower your kP by clicking "Yes"'

    }
}
if(technique === 'Oscillations') {
    if(finalButtonPresses === 0) {
    warning = 'Your robot should not be unstable in this stage! I have not changed any values for you. Lower your kP by clicking "Yes"'
    
    }
       if(finalButtonPresses === 1) {
                    delta = delta * 0.9;
                    kDelta = kDelta * 4;
            kPMath.push(kPMath[iteration] * 0.9).toFixed(3);
            kDMath.push(kDMath[iteration]).toFixed(3);
            iteration++;
                warning = 'I have decreased kP by 10%, but if your robot was not unstable before it wouldnt make sense for it to be unstable now when the kD is higher. Please check your robot and/or PID implementation.'

    } 
}

}

function reset() {
    warning = '';
        techniqueStarted = false;
        showFinalResults = false;
        technique = "None";
        iteration = 0;
        firstNo = false;
        guess = 0;
        kD = 0;
        kDMath = [0];
        kPMath = [0];
        delta = 0;
        kDelta = 0;
        finalButtonPresses = 0;
}
let finalButtonPresses = $state(0);
function Done() {
            finalButtonPresses++;
            warning = '';

    if(technique === 'kP First') {
    showFinalResults = true;
    }
    if(technique === 'kD First') {
        if(finalButtonPresses === 1) {
        lastButton = 'Refine kP';
        firstNo = true;
        firstYes = true;
        action = 'tip, drift, or turn at the end of the motion';
        flavor = 'Now, try to find the highest kD value possible with our robot. For this motion, drive the robot a larger distance like 72 inches';

        }
    if(finalButtonPresses === 2) {
        delta = guess / 4;
        lastButton = 'Done';
        firstNo = true;
        firstYes = true;
        action = 'oscillate';
        flavor = 'Refine kP to be as high as possible with our given kD value. Return the drive distance to the original value for this motion.';
    }
    if(finalButtonPresses === 3) {
        showFinalResults = true;
    }
        }
    if(technique === 'Oscillations') {
        if(finalButtonPresses === 1) {
        action = 'oscillate at all'
        lastButton = 'Done';
        firstNo = true;
        firstYes = true;
        flavor = 'Now, try to find the lowest kD value possible that still prevents oscillations.';
        }
                if(finalButtonPresses === 2) {
                    showFinalResults = true;
        }
    }
}


</script>

<div class="text-transparent text-8xl justify-self-center font-bold my-2 font-main scale-y-105 bg-clip-text title px-2">
    PID Tuner

</div>
    <div class="glowWrapper text-transparent text-8xl font-bold my-2 font-main scale-y-105 px-2 absolute top-0 left-1/2">
        PID Tuner
</div>

<Header text="Welcome!" />

<h1 class="text-md font-main text-white my-2">
    This webpage is specifically designed to aid new programmers in the VEX Robotics competition in tuning their PID controller. Pick a technique based on your robot's limitations and then give an initial guess. Using <a class="link bg-clip-text text-transparent underline" href="https://en.wikipedia.org/wiki/Binary_search">binary search</a>, our algorithm will try to find the most optimal PID values for you for each iteration. All you have to do is to look at the robot for any oscillations. 
</h1>

<Header text="Pilot's Checklist" />
<h1 class="text-md font-main text-white my-2">
    Before starting, make sure to zero all other values in your PID controller. This means:
    <ul class="list-disc pl-8">
        <li>kD and kI are 0</li>
        <li>Error ranges and timeouts are 0</li>
        <li>In the movement you call, make sure the timeout won't stop the motion midway <br>
        Example (LemLib): chassis.moveToPoint(0, 24, <span class="font-bold">4000</span>);</li>

    </ul>
</h1>

<Header text="Choose a Technique" />

<h1 class="text-md font-main text-white my-2">
    Pick a technique that best tailors to your bot! <br>
    <ul>
        <li><span class="link bg-clip-text font-bold text-transparent">Oscillations:</span> Simplest to tune; increase kP as high as possible while staying below a set amount of oscillations. With my experience, I would do max 1 oscillation for lateral and 3 for angular.</li>
        <li><span class="link bg-clip-text font-bold text-transparent">kD First:</span> This is explicitly for tuning lateral PID and for robots that tip, swerve, or rotate when driving fast and then braking hard.</li>
    </ul>
</h1>

<div class="bg-black/25 flex justify-evenly">
<!--
<button disabled={techniqueStarted} onclick={() => pickTechnique('kP First')} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[20%] font-main text-white text-xl my-2 bg-main hover:w-[22%] text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    kP First
</button>
-->
<button disabled={techniqueStarted} onclick={() => pickTechnique('Oscillations')} class=" ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[20%] font-main text-white text-xl my-2 bg-main hover:w-[22%] text-center hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    Oscillations
</button>

<button disabled={techniqueStarted} onclick={() => pickTechnique('kD First')} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[20%] font-main text-white text-xl my-2 bg-main hover:w-[22%] text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    kD First
</button>


</div>

{#if technique !== "None"}
<div class="guessDisplay my-4">
<Header text="Guess kP & kD"/>
<h1 class="text-md font-main text-white my-2">
    Input a starting guess for your PID Controller. We need a starting kP value, plus a starting kD value for the future. You don't need an accurate guess to get good results! It just speeds up the process.  A good starting value for a 6 motor drive train thats around 10 lbs is 5 kP and 20 kD for lateral and 2 kP and 20 kD for angular. 
</h1>
<div class="flex items-center p-2 rounded-lg w-fit">
<button disabled={techniqueStarted} class="link w-5 h-7 pb-0.75 align-middle text-center mr-0" aria-label="Increment Guess" onclick={() => guess++}>+</button>
<input disabled={techniqueStarted} bind:value={guess} placeholder="Guess a kP Value" class="h-7 text-white bg-main text-xl my-4 font-main w-12 ml-1 pl-1 mr-0" type="number" />
<button disabled={techniqueStarted} class="link w-5 h-7 pb-0.75 align-middle text-center ml-1" aria-label="Increment Guess" onclick={() => {if(guess >= 1) guess--}}>-</button>
<h1 class="ml-2 text-xl font-main text-white my-2">
kP
</h1>
</div>

<div  class="flex items-center p-2 -my-9  rounded-lg w-fit">
<button disabled={techniqueStarted} class="link w-5 h-7 pb-0.75 align-middle text-center mr-0" aria-label="Increment Guess" onclick={() => kD++}>+</button>
<input disabled={techniqueStarted} bind:value={kD} placeholder="Guess a kP Value" class="h-7 text-white bg-main text-xl my-4 font-main w-12 ml-1 pl-1 mr-0" type="number" />
<button disabled={techniqueStarted} class="link w-5 h-7 pb-0.75 align-middle text-center ml-1" aria-label="Increment Guess" onclick={() => {if(kD >= 1) kD--}}>-</button>
<h1 class="ml-2 text-xl font-main text-white my-2">
kD (later)
</h1>
</div>

{#if (guess > 0  && kD > 0)} 
<h1 class="text-lg font-main text-white my-5">

Start with
    <span class="font-main link bg-clip-text text-transparent text-2xl ">
 {guess}
    </span>
     kP and
         <span class="font-main link bg-clip-text text-transparent text-2xl ">
 {kD}
    </span>
    kD with technique "<span class="font-main link bg-clip-text text-transparent text-2xl ">
{technique}
    </span>"?
     </h1>
{#if !techniqueStarted}
     <button onclick={() => startTechnique()} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[20%] font-main text-white text-xl my-2 bg-main hover:text-2xl text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    Affirmative
</button>
{/if}
{/if}
</div>
{/if}

{#if techniqueStarted}
<div class="techniqueProcessDisplay">
<Header text={technique} />

<h1 class="text-md font-main my-2 text-white">
    Iteration {iteration + 1}: <br>
    {iteration >= 75 ? 'That is a lot of iterations... How much sig figs are you going for??' : ''}
</h1>
<h1 class="text-lg font-main my-2 text-red-200">
    {warning}
</h1>
<h1 class="text-lg font-main text-white my-2">
    {flavor}
</h1>
<h1 class="text-lg font-main text-white my-2">
    Did the robot {action} with     <span class="font-main link bg-clip-text text-transparent text-2xl ">
 {kPMath[iteration]}
    </span> kP and 
    <span class="font-main link bg-clip-text text-transparent text-2xl ">
    {kDMath[iteration]}
    </span> kD?

         <button onclick={() => Yes()} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[15%] font-main text-white text-xl my-2 bg-main hover:text-2xl text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    Yes
</button>

     <button onclick={() => No()} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[15%] font-main text-white text-xl my-2 bg-main hover:text-2xl text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    No
</button>


     <button onclick={() => Unstable()} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[10%] font-main text-white text-xl my-2 bg-main hover:text-2xl text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    Unstable
</button>


     <button onclick={() => Done()} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[10%] font-main text-white text-xl my-2 bg-main hover:text-2xl text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    {lastButton}
</button>
</h1>

{#if showFinalResults}
<h1 class="text-lg font-main text-white my-2">
    Final Results:     <span class="font-main link bg-clip-text text-transparent text-2xl ">
 {kPMath[iteration]} kP </span>
    and 
    <span class="font-main link bg-clip-text text-transparent text-2xl ">
    {kDMath[iteration]} kD
    </span> 

         <button onclick={() => reset()} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[33%] font-main text-white text-xl my-2 bg-main hover:text-2xl text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    Reset
</button>
</h1>
{/if}
</div>
{/if}
<footer class="flex justify-center">
    <a href="https://uigalaxy.net" class=" text-center underline font-main link bg-clip-text text-transparent text-xl  watermark ">
 uigalaxy.net 2026 | 45434X Paradox
 </a>
</footer>

