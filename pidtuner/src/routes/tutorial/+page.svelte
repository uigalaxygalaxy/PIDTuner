<script>
	import Header from '$lib/Header.svelte';
</script>

<a href="/" class="relative flex justify-center">
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
</a>
<Header text="Brief" />
<h1 class="text-md my-2 font-main text-white">
	PID Tuner finds optimal PID values using Yes/No questions to help new comers tune their PID
	controller they coded themselves or from a template.
	<br /> <br />

	Teams that do not have a PID controller can use online templates like
	<a
		class="link bg-clip-text text-transparent underline"
		href="https://github.com/JacksonAreaRobotics/JAR-Template">JAR</a
	>
	or
	<a class="link bg-clip-text text-transparent underline" href="https://github.com/LemLib/LemLib"
		>LemLib</a
	>
	that incorporate these concepts into an easy-to-use template.

	<br /> <br />
	It is strongly advised that students learn the basics of what a PID controller is if they choose to
	implement it into their robot in order to follow the REC Foundation's Student-Centered Policy:
</h1>
<h2 class="mb-2 font-main text-sm text-gray-200 italic">
	"Teams that utilize example code or custom libraries from outside sources should use caution. The
	program used should represent the students’ efforts and abilities, and libraries must be publicly
	available. Blindly using code without understanding the code functionality is not consistent with
	the educational goals of this program. Students should be able to understand and explain the code,
	and students should be able to demonstrate that they can program on a level equivalent to the code
	used in their mechanism." <a
		class="link bg-clip-text text-transparent underline"
		href="https://kb.roboticseducation.org/hc/en-us/articles/5449868745367-Student-Centered-Policy"
	>
		- REC Foundation</a
	>
</h2>
<Header text="Guessing values" />
<h1 class="text-md my-2 font-main text-white">
	Use starting values to help jumpstart the process of PID tuning! If you do not have a guess,
	compare the weight and motors to our example values and create a rough estimate. You do not need
	to be accurate at all!
</h1>
<Header text="Oscillations" />
<h1 class="text-md my-2 font-main text-white">
	Oscillations is a technique where you increase kP to be as high as possible while oscillating
	(overshooting and undershooting) below a set amount of times. You then increase kD until the
	oscillations stop.
	<br />
	The amount of oscillations to stop at depends on the type of PID and you! The more oscillations you
	pick, the higher chance you will have a unstable PID controller where any value of kD won't stop oscillations
	with your kP value.
</h1>
<Header text="kD First" />
<h1 class="text-md my-2 font-main text-white">
	The kD First technique is made for robots that tend to tip or turn at the end of long lateral
	motions, causing the limiting factor to be kD instead of kP. This is typically due to large kD
	values causing rapid stopping. In this technique, we:
	<ul class="list-disc pl-8">
		<li>
			roughly tune our kP value and kD value with a normal driving distance where the robot can't
			speed up enough to drift. If your guess for kD is too large, click Unstable to lower kD by
			10%. You can click the Update button to update values and deltas.
		</li>
		<li>
			find the largest kD value possible with our kP value so that the robot can drive long
			distances without tipping or drifting. Since we're lowering kD, your robot may start to
			overshoot. Click the 'Unstable' button to lower kP by 10%. You can click the Update button to
			update values and deltas.
		</li>
		<li>
			find the largest possible kP value possible without our robot oscillating. This gives us our
			biggest possible kP value with our biggest possible kD value while preventing robot tips on
			large drives.
		</li>
	</ul>
</h1>
