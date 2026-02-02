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
	let updateGuess = $state(0);
	let updateKD = $state(0);
	let updateDelta = $state(0);
	let updateKDelta = $state(0);
	let techniqueStarted = $state(false);
	let firstNo = $state(true);
	let firstYes = $state(true);
	let action = $state('oscillate');
	let flavor = $state(
		'Start with finding a rough estimate for kP. As long as it is high enough that it will slow down fast without oscillating will be enough for our purposes.'
	);
	let lastButton = $state('Find kD');
	let letUpdate = $state(false);

	let showFinalResults = $state(false);
	let warning = $state('');

	function pickTechnique(choice: string) {
		technique = choice;
	}

	function startUpdate() {
		updateGuess = kPMath[iteration];
		updateKD = kDMath[iteration];
		updateKDelta = kDelta;
		updateDelta = delta;
		letUpdate = true;
	}

	function update() {
		letUpdate = false;
		iteration++;
		kPMath.push(Number(updateGuess.toFixed(3)));
		kDMath.push(Number(updateKD.toFixed(3)));
		kDelta = Number(updateKDelta.toFixed(4));
		delta = Number(updateDelta.toFixed(4));
	}

	function startTechnique() {
		techniqueStarted = true;
		if (technique === 'kP First') {
			kDelta = kD;
			kDMath = [0];
			kPMath = [guess];
			delta = kPMath[0];
		}
		if (technique === 'kD First') {
			action = 'oscillate';
			kDelta = kD;
			kDMath = [kD];
			kPMath = [guess];
			delta = kPMath[0];
			flavor =
				'Start with finding a rough estimate for kP. As long as it is high enough that it will slow down fast without oscillating will be enough for our purposes.';
		}
		if (technique === 'Oscillations') {
			action = 'oscillate above a set amount of times';
			flavor =
				'Find the highest kP value you can while staying below a set amount of oscillations.';
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
			kPMath.push(Number(kPMath[iteration].toFixed(3)));
			kDMath.push(Number((kDelta + kDMath[iteration]).toFixed(3)));
			iteration++;
		}
		if (technique === 'kD First') {
			if (finalButtonPresses === 0) {
				delta = delta / 2;
				kPMath.push(Number((kPMath[iteration] - delta).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 1) {
				kDelta = kDelta / 2;
				kPMath.push(Number(kPMath[iteration].toFixed(3)));
				kDMath.push(Number((kDMath[iteration] - kDelta).toFixed(3)));
				iteration++;
			}

			if (finalButtonPresses === 2) {
				if (!firstYes) {
					delta = delta / 2;
				}
				kPMath.push(Number((kPMath[iteration] - delta).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
		}
		if (technique === 'Oscillations') {
			if (finalButtonPresses === 0) {
				delta = delta / 2;
				kPMath.push(Number((kPMath[iteration] - delta).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 1) {
				if (!firstYes) {
					kDelta = kDelta / 2;
				}
				kPMath.push(Number(kPMath[iteration].toFixed(3)));
				kDMath.push(Number((kDMath[iteration] + kDelta).toFixed(3)));
				iteration++;
			}
		}
	}

	function No() {
		if (firstYes) {
			firstYes = false;
		}
		if (technique === 'kP First') {
			kPMath.push(Number((delta + kPMath[iteration]).toFixed(3)));
			kDMath.push(Number(kDMath[iteration].toFixed(3)));
			iteration++;
		}
		if (technique === 'kD First') {
			if (finalButtonPresses === 0) {
				if (!firstNo) {
					delta = delta / 2;
				}
				kPMath.push(Number((kPMath[iteration] + delta).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 1) {
				if (!firstNo) {
					kDelta = kDelta / 2;
				}
				kPMath.push(Number(kPMath[iteration].toFixed(3)));
				kDMath.push(Number((kDMath[iteration] + kDelta).toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 2) {
				if (!firstNo) {
					delta = delta / 2;
				}
				kPMath.push(Number((kPMath[iteration] + delta).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
		}
		if (technique === 'Oscillations') {
			if (finalButtonPresses === 0) {
				if (!firstNo) {
					delta = delta / 2;
				}
				kPMath.push(Number((kPMath[iteration] + delta).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 1) {
				kDelta = kDelta / 2;

				kPMath.push(Number(kPMath[iteration].toFixed(3)));
				kDMath.push(Number((kDMath[iteration] - kDelta).toFixed(3)));
				iteration++;
			}
		}
	}

	function Unstable() {
		if (technique === 'kP First') {
			delta = delta / 2;
			kDelta = kDelta / 2;
			kPMath.push(Number((kPMath[iteration] - delta).toFixed(3)));
			kDMath.push(Number((kDMath[iteration] - kDelta).toFixed(3)));
			iteration++;
			delta = delta / 2;
		}
		if (technique === 'kD First') {
			if (finalButtonPresses === 0) {
				kDelta = kDelta * 0.9;
				kPMath.push(Number(kPMath[iteration].toFixed(3)));
				kDMath.push(Number((kDMath[iteration] * 0.8).toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 1) {
				delta = delta * 0.9;
				kPMath.push(Number((kPMath[iteration] * 0.9).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
			}
			if (finalButtonPresses === 2) {
				warning =
					'Your robot should not be unstable in this stage! I have not changed any values for you. Lower your kP by clicking "Yes"';
			}
		}
		if (technique === 'Oscillations') {
			if (finalButtonPresses === 0) {
				warning =
					'Your robot should not be unstable in this stage! I have not changed any values for you. Lower your kP by clicking "Yes"';
			}
			if (finalButtonPresses === 1) {
				delta = delta * 0.9;
				kDelta = kDelta * 4;
				kPMath.push(Number((kPMath[iteration] * 0.9).toFixed(3)));
				kDMath.push(Number(kDMath[iteration].toFixed(3)));
				iteration++;
				warning =
					'I have decreased kP by 10%, but if your robot was not unstable before it wouldnt make sense for it to be unstable now when the kD is higher. Please check your robot and/or PID implementation.';
			}
		}
	}

	function reset() {
		warning = '';
		techniqueStarted = false;
		showFinalResults = false;
		technique = 'None';
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

		if (technique === 'kP First') {
			showFinalResults = true;
		}
		if (technique === 'kD First') {
			if (finalButtonPresses === 1) {
				lastButton = 'Refine kP';
				firstNo = true;
				firstYes = true;
				action = 'tip, drift, or turn at the end of the motion';
				flavor =
					'Now, try to find the highest kD value possible with our robot. For this motion, drive the robot a larger distance like 72 inches';
			}
			if (finalButtonPresses === 2) {
				delta = guess / 4;
				lastButton = 'Done';
				firstNo = true;
				firstYes = true;
				action = 'oscillate';
				flavor =
					'Refine kP to be as high as possible with our given kD value. Return the drive distance to the original value for this motion.';
			}
			if (finalButtonPresses === 3) {
				showFinalResults = true;
			}
		}
		if (technique === 'Oscillations') {
			if (finalButtonPresses === 1) {
				action = 'oscillate at all';
				lastButton = 'Done';
				firstNo = true;
				firstYes = true;
				flavor = 'Now, try to find the lowest kD value possible that still prevents oscillations.';
			}
			if (finalButtonPresses === 2) {
				showFinalResults = true;
			}
		}
	}
</script>

<div
	class="title my-2 scale-y-105 justify-self-center bg-clip-text px-2 font-main text-8xl font-bold text-transparent"
>
	PID Tuner
</div>
<div
	class="glowWrapper absolute top-0 left-1/2 my-2 scale-y-105 px-2 font-main text-8xl font-bold text-transparent"
>
	PID Tuner
</div>

<Header text="Welcome!" />

<h1 class="text-md my-2 font-main text-white">
	This webpage is specifically designed to aid new programmers in the VEX Robotics competition in
	tuning their PID controller. Pick a technique based on your robot's limitations and then give an
	initial guess. Using <a
		class="link bg-clip-text text-transparent underline"
		href="https://en.wikipedia.org/wiki/Binary_search">binary search</a
	>, our algorithm will try to find the most optimal PID values for you for each iteration. All you
	have to do is to look at the robot for any oscillations. <br />
	For more information, view our
	<a class="link bg-clip-text text-transparent underline" href="/tutorial">tutorial!</a> <br />
</h1>

<Header text="Pilot's Checklist" />
<h1 class="text-md my-2 font-main text-white">
	Before starting, make sure to zero all other values in your PID controller. This means:
	<ul class="list-disc pl-8">
		<li>kD and kI are 0</li>
		<li>Error ranges and timeouts are 0</li>
		<li>
			In the movement you call, make sure the timeout won't stop the motion midway <br />
			Example (LemLib): chassis.moveToPoint(0, 24, <span class="font-bold">4000</span>);
		</li>
	</ul>
</h1>

<Header text="Choose a Technique" />

<h1 class="text-md my-2 font-main text-white">
	Pick a technique that best tailors to your bot! <br />
	<ul>
		<li>
			<span class="link bg-clip-text font-bold text-transparent">Oscillations:</span> Simplest to tune;
			increase kP as high as possible while staying below a set amount of oscillations. With my experience,
			I would do max 1 oscillation for lateral and 3 for angular.
		</li>
		<li>
			<span class="link bg-clip-text font-bold text-transparent">kD First:</span> This is explicitly for
			tuning lateral PID and for robots that tip, swerve, or rotate when driving fast and then braking
			hard.
		</li>
	</ul>
</h1>

<div class="flex justify-evenly bg-black/25">
	<!--
<button disabled={techniqueStarted} onclick={() => pickTechnique('kP First')} class="ease-cubic-bezier(.17,.67,.48,1.14) duration-200 techniqueButton w-[20%] font-main text-white text-xl my-2 bg-main hover:w-[22%] text-center  hover:border-b-4 active:border-b-0 active:translate-y-0.5">
    kP First
</button>
-->
	<button
		disabled={techniqueStarted}
		onclick={() => pickTechnique('Oscillations')}
		class=" ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[20%] bg-main text-center font-main text-xl text-white duration-200 hover:w-[22%] hover:border-b-4 active:translate-y-0.5 active:border-b-0"
	>
		Oscillations
	</button>

	<button
		disabled={techniqueStarted}
		onclick={() => pickTechnique('kD First')}
		class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[20%] bg-main text-center font-main text-xl text-white duration-200 hover:w-[22%] hover:border-b-4 active:translate-y-0.5 active:border-b-0"
	>
		kD First
	</button>
</div>

{#if technique !== 'None'}
	<div class="guessDisplay my-4">
		<Header text="Guess kP & kD" />
		<h1 class="text-md my-2 font-main text-white">
			Input a starting guess for your PID Controller. We need a starting kP value, plus a starting
			kD value for the future. You don't need an accurate guess to get good results! It just speeds
			up the process. A good starting value for a 6 motor drive train thats around 10 lbs is 5 kP
			and 20 kD for lateral and 2 kP and 20 kD for angular.
		</h1>
		<div class="flex w-fit items-center rounded-lg p-2">
			<button
				disabled={techniqueStarted}
				class="link mr-0 h-7 w-5 pb-0.75 text-center align-middle"
				aria-label="Increment Guess"
				onclick={() => guess++}>+</button
			>
			<input
				disabled={techniqueStarted}
				bind:value={guess}
				placeholder="Guess a kP Value"
				class="my-4 mr-0 ml-1 h-7 w-12 bg-main pl-1 font-main text-xl text-white"
				type="number"
			/>
			<button
				disabled={techniqueStarted}
				class="link ml-1 h-7 w-5 pb-0.75 text-center align-middle"
				aria-label="Increment Guess"
				onclick={() => {
					if (guess >= 1) guess--;
				}}>-</button
			>
			<h1 class="my-2 ml-2 font-main text-xl text-white">kP</h1>
		</div>

		<div class="-my-9 flex w-fit items-center rounded-lg p-2">
			<button
				disabled={techniqueStarted}
				class="link mr-0 h-7 w-5 pb-0.75 text-center align-middle"
				aria-label="Increment Guess"
				onclick={() => kD++}>+</button
			>
			<input
				disabled={techniqueStarted}
				bind:value={kD}
				placeholder="Guess a kP Value"
				class="my-4 mr-0 ml-1 h-7 w-12 bg-main pl-1 font-main text-xl text-white"
				type="number"
			/>
			<button
				disabled={techniqueStarted}
				class="link ml-1 h-7 w-5 pb-0.75 text-center align-middle"
				aria-label="Increment Guess"
				onclick={() => {
					if (kD >= 1) kD--;
				}}>-</button
			>
			<h1 class="my-2 ml-2 font-main text-xl text-white">kD (later)</h1>
		</div>

		{#if guess > 0 && kD > 0}
			<h1 class="my-5 font-main text-lg text-white">
				Start with
				<span class="link bg-clip-text font-main text-2xl text-transparent">
					{guess}
				</span>
				kP and
				<span class="link bg-clip-text font-main text-2xl text-transparent">
					{kD}
				</span>
				kD with technique "<span class="link bg-clip-text font-main text-2xl text-transparent">
					{technique}
				</span>"?
			</h1>
			{#if !techniqueStarted}
				<button
					onclick={() => startTechnique()}
					class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[20%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
				>
					Affirmative
				</button>
			{/if}
		{/if}
	</div>
{/if}

{#if techniqueStarted}
	<div class="techniqueProcessDisplay">
		<Header text={technique} />

		<h1 class="text-md my-2 font-main text-white">
			Iteration {iteration + 1}: <br />
			{iteration >= 75
				? 'That is a lot of iterations... How much sig figs are you going for??'
				: ''}
		</h1>
		<h1 class="my-2 font-main text-lg text-red-200">
			{warning}
		</h1>
		<h1 class="my-2 font-main text-lg text-white">
			{flavor}
		</h1>
		<h1 class="my-2 font-main text-lg text-white">
			Did the robot {action} with
			<span class="link bg-clip-text font-main text-2xl text-transparent">
				{kPMath[iteration]}
			</span>
			kP and
			<span class="link bg-clip-text font-main text-2xl text-transparent">
				{kDMath[iteration]}
			</span>
			kD?

			<button
				onclick={() => Yes()}
				class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[15%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
			>
				Yes
			</button>

			<button
				onclick={() => No()}
				class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[15%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
			>
				No
			</button>

			<button
				onclick={() => Unstable()}
				class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[10%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
			>
				Unstable
			</button>

			<button
				onclick={() => Done()}
				class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[10%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
			>
				{lastButton}
			</button>

			<button
				onclick={() => startUpdate()}
				class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[10%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
			>
				Update
			</button>
		</h1>

		{#if letUpdate}
			<div class="flex w-fit items-center rounded-lg p-2">
				<div class="w-100% items-center justify-center">
					<div>
						<button
							class="link mr-0 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => updateGuess++}>+</button
						>
						<input
							bind:value={updateGuess}
							placeholder="Guess a kP Value"
							class="my-4 mr-0 ml-1 h-7 w-30 bg-main pl-1 font-main text-xl text-white"
							type="number"
						/>
						<button
							class="link ml-1 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => {
								if (updateGuess >= 1) updateGuess--;
							}}>-</button
						>
						<h1 class="my-2 ml-2 font-main text-xl text-white">kP</h1>
					</div>
					<div class="mr-5">
						<button
							class="link mr-0 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => (updateDelta += 0.1)}>+</button
						>
						<input
							bind:value={updateDelta}
							placeholder="Guess a kP Value"
							class="my-4 mr-0 ml-1 h-7 w-30 bg-main pl-1 font-main text-xl text-white"
							type="number"
						/>
						<button
							class="link ml-1 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => {
								if (updateDelta >= 0.1) updateDelta -= 0.1;
							}}>-</button
						>
						<h1 class="my-2 ml-2 font-main text-xl text-white">kP change-by</h1>
					</div>
				</div>

				<div>
					<div>
						<button
							class="link mr-0 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => updateKD++}>+</button
						>
						<input
							bind:value={updateKD}
							placeholder="Guess a kP Value"
							class="my-4 mr-0 ml-1 h-7 w-30 bg-main pl-1 font-main text-xl text-white"
							type="number"
						/>
						<button
							class="link ml-1 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => {
								if (updateKD >= 1) updateKD--;
							}}>-</button
						>
						<h1 class="my-2 ml-2 font-main text-xl text-white">kD</h1>
					</div>
					<div>
						<button
							class="link mr-0 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => (updateKDelta += 0.1)}>+</button
						>
						<input
							bind:value={updateKDelta}
							placeholder="Guess a kP Value"
							class="my-4 mr-0 ml-1 h-7 w-30 bg-main pl-1 font-main text-xl text-white"
							type="number"
						/>
						<button
							class="link ml-1 h-7 w-5 pb-0.75 text-center align-middle"
							aria-label="Increment Guess"
							onclick={() => {
								if (updateKDelta >= 0.1) updateKDelta -= 0.1;
							}}>-</button
						>
						<h1 class="my-2 ml-2 font-main text-xl text-white">kD change-by</h1>
					</div>
				</div>
			</div>
			<button
				onclick={() => update()}
				class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[20%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
			>
				Update
			</button>
		{/if}

		{#if showFinalResults}
			<h1 class="my-2 font-main text-lg text-white">
				Final Results: <span class="link bg-clip-text font-main text-2xl text-transparent">
					{kPMath[iteration]} kP
				</span>
				and
				<span class="link bg-clip-text font-main text-2xl text-transparent">
					{kDMath[iteration]} kD
				</span>

				<button
					onclick={() => reset()}
					class="ease-cubic-bezier(.17,.67,.48,1.14) techniqueButton my-2 w-[33%] bg-main text-center font-main text-xl text-white duration-200 hover:border-b-4 hover:text-2xl active:translate-y-0.5 active:border-b-0"
				>
					Reset
				</button>
			</h1>
		{/if}
	</div>
{/if}
<footer class="flex justify-center">
	<a
		href="https://uigalaxy.net"
		class=" link watermark bg-clip-text text-center font-main text-xl text-transparent underline"
	>
		uigalaxy.net 2026 | 45434X Paradox
	</a>
</footer>
