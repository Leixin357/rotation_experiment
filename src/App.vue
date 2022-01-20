<template>
	<Experiment title="Mental Rotation Experiment">
		<InstructionScreen :title="'Welcome'">
			Welcome to the mental rotation experiment!
		</InstructionScreen>

		<InstructionScreen :title="'General Instructions'">
			Please read the following instruction before you start the experiment:
			<br />
			<br />
			Please compare each pair of pictures and decide they are
			<br />
			<br />
				<li>the same object</li>
				<li>different objects</li>
			<br /> 
			If they are the same object, it should be able to get the same image after rotation.
			If rotation cannot result in the same image, they are different objects.
			<br /> 
			<br />
			Please press "f" on the keyboard if you think they are the same object, press "j" if they are different.
			<br /> 
			<br />
			If you are ready, please press NEXT to start the experiment
		</InstructionScreen>
		<!-- Here we create screens in a loop for every entry in training_trials -->
    <template v-for="(trial, i) of training_trials">
			<KeypressScreen
				question="Are they the same object or different objects?"
				:feedback-time="800"
				:fixation-time="getRandomFixationTime()"
				:response-time="7500"
				:keys="{'f': 'same', 'j': 'different'}"
				:key="i"
			>
				<template #stimulus>
					<img :src="trial.picture" />
					<Record :data="{
						angle: trial.angle,
						expected_answer: trial.expected,
						item_num: trial.item,
						trial_type: 'training',
						trial_num: i+1,
					}"/>
				</template>
				<template #feedback>
					<p v-if="!$magpie.measurements.hasOwnProperty('response')">Faster!</p>
					<p v-else-if="$magpie.measurements.response == trial.expected">Correct!</p>
					<p v-else>Wrong!</p>
				</template>
			</KeypressScreen>
		</template>


		<PostTestScreen />

		<!-- While developing your experiment, using the DebugResults screen is fine,
			once you're going live, you can use the <SubmitResults> screen to automatically send your experimental data to the server. -->
		<SubmitResultsScreen />
	</Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import main_trials from '../trials/mr_main_trials.csv';
import training_trials from '../trials/mr_training_trials.csv';
import _ from 'lodash';
export default {
	name: 'App',
	data() {
		return {
			main_trials: _.shuffle(main_trials),
			training_trials: _.shuffle(training_trials),
		};
	},
	methods: {
		getRandomFixationTime() {
				return _.sample([300, 350, 320, 310, 280])
		}
	}
};
</script>