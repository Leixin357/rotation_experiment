<template>
	<Experiment title="Mental Rotation Experiment">
		<InstructionScreen :title="'Welcome'">
			Welcome, participants of the mental rotation experiment!
		</InstructionScreen>

		<InstructionScreen :title="'General Instructions'">
			This is a sample instructions view.
			<br />
			<br />
			Add instructions here...
		</InstructionScreen>
		<!-- Here we create screens in a loop for every entry in training_trials -->
    <template v-for="(trial, i) of training_trials">
			<KeypressScreen
				question="Are these two figures the same except for their orientation?"
				:feedback-time="800"
				:fixation-time="getRandomFixationTime()"
				:response-time="7500"
				:keys="{
					'f':'same',
					'j':'different'
				}"
			>
				<template #stimulus>
					<img :src="trial.picture" />
					<Record :data="{
						angle: trial.angle,
						expected_answer: trial.expected,
						item_number: trial.item,
						trial_type: 'training',
						trial_number: i+1,
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