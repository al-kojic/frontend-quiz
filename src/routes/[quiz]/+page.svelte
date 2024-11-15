<script>
	import { page } from '$app/stores';
	import ListItem from '$lib/components/ListItem.svelte';
	let { data } = $props();
	let currentQuestion = $state(0);
	let selectedAnswer = $state('');
	let score = $state(0);
	let progressPercentage = $state(10);
	let submitted = $state(false);

	function handleSubmit() {
		submitted = true;
		data.questions[currentQuestion].selectedAnswer = selectedAnswer;
		if (selectedAnswer === data.questions[currentQuestion].answer) {
			score++;
		}

		progressPercentage = ((currentQuestion + 1) / data.questions.length) * 100;
	}

	function nextQuestion() {
		submitted = false;
		currentQuestion++;
		selectedAnswer = '';
	}
</script>

<progress class="progress progress-primary w-56" value={progressPercentage} max="100"></progress>

{#if currentQuestion < data.questions.length}
	{#each data.questions as question, index (question.question)}
		{#if index === currentQuestion}
			<p>Question {index + 1} of {data.questions.length}</p>
			<h2>{question.question}</h2>
			{#each question.options as option, index (option)}
				{#key submitted}
					<ListItem
						title={option}
						{index}
						correctAnswer={question.selectedAnswer && question.answer === option}
						incorrectlySelected={question.selectedAnswer === option && question.answer !== option}
						focusAction={() => (selectedAnswer = option)}
						disabled={submitted}
					></ListItem>
				{/key}
			{/each}
		{/if}
	{/each}

	{#if !submitted}
		<button class="btn btn-primary" onclick={handleSubmit} disabled={!selectedAnswer}>Submit</button
		>
	{:else}
		<button class="btn btn-primary" onclick={nextQuestion}>Next Question</button>
	{/if}
{:else}
	<h2>Game Over!</h2>
	<p>You scored {score} out of {data.questions.length} points!</p>
	<button class="btn btn-primary" onclick={window.location.reload()}> Play again!</button>
{/if}
