<script lang="ts">
	import { goto } from "$app/navigation";
	import { CreateBetWithOutcomes } from "../../logic/betCreation";
	import autosize from "svelte-autosize";

	let betName = "";
	let betDescription = "";
	let closedAt: Date = new Date();
	let outcomes: string[] = ["", ""];

	let errorMessage: string = "";

	function checkInput(): boolean {
		if (betName == "") {
			errorMessage = "Нужно заполнить наименование";
			return false;
		}
		if (outcomes.length < 2) {
			errorMessage = "Нужно добавить хотя бы два исхода";
			return false;
		}

		for (let i = 0; i < outcomes.length; i++) {
			if (outcomes[i] == "") {
				errorMessage = "Нужно заполнить все исходы";
				return false;
			}
		}

		return true;
	}

	let handleCreateBet = async () => {
		try {
			if (!checkInput()) return;
			let bet = await CreateBetWithOutcomes(betName, betDescription, closedAt, outcomes);
			errorMessage = "";
			// Go to bet page
			goto(`/bet/${bet.id}`);
		} catch (e) {
			("Неверно заполнены поля");
			console.log(e);
		}
	};
</script>

<div class="form">
	<div class="loginform">
		<h1 class="loginform__title">Создать пари</h1>
		<input class="loginform__input" type="text" placeholder="Наименование" bind:value={betName} />
		<textarea
			class="loginform__textarea"
			use:autosize
			placeholder="Описание"
			bind:value={betDescription}
		/>

		{#each outcomes as outcome, i}
			<div class="outcome__input">
				<input class="outcome__input__field" type="text" placeholder="Исход" bind:value={outcome} />
				{#if i > 1}
					<button
						class="outcome__deletion__button"
						on:click={() => {
							outcomes.splice(i, 1);
							outcomes = outcomes;
						}}
					>
						x
					</button>
				{:else}
					<!-- <div class="outcome__deletion__button" /> -->
				{/if}
			</div>
		{/each}
		<button
			class="loginform__button"
			on:click={() => {
				outcomes.push("");
				outcomes = outcomes;
			}}
		>
			Добавить исход
		</button>

		<button class="loginform__button" on:click={handleCreateBet}>Создать</button>

		{#if errorMessage != ""}
			<p class="loginform__error">{errorMessage}</p>
		{/if}
	</div>
</div>

<style>
	.form {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.loginform {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		border-radius: 10px;

		background-color: var(--betme-yellow);
		margin-top: 2%;
	}

	.loginform__title {
		font-size: 2rem;
		font-family: "Montserrat", sans-serif;
	}

	.loginform__input {
		width: 20rem;
		min-height: 2rem;
		margin: 0.5rem;

		font-family: "Montserrat", sans-serif;

		background-color: var(--betme-black);
		color: var(--betme-yellow);
		border-width: 0;
		border-radius: 5px;
	}

	.loginform__textarea {
		width: 20rem;
		min-height: 2rem;

		font-family: "Montserrat", sans-serif;
		font-size: 14px;

		background-color: var(--betme-black);
		color: var(--betme-yellow);
		border-width: 0;
		border-radius: 5px;
	}

	.loginform__textarea::placeholder {
		vertical-align: middle;
	}

	.loginform__button {
		width: 20rem;
		height: 2rem;
		margin: 0.5rem;

		background-color: var(--betme-black);
		color: var(--betme-yellow);
		border-radius: 10px;
		border: none;

		font-family: "Montserrat", sans-serif;
	}

	.outcome__input {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: flex-start;
		height: 100%;
		width: 100%;
		margin: 0 10px 0 10px;
	}

	.outcome__input__field {
		width: 100%;
		height: 2rem;
		margin: 0.5rem;

		display: flex;
		align-self: flex-start;

		font-family: "Montserrat", sans-serif;
		background-color: var(--betme-black);
		color: var(--betme-yellow);
		border-width: 0;
		border-radius: 5px;
	}

	.outcome__deletion__button {
		width: 2rem;
		height: 2rem;
		margin-right: 0.25rem;
		margin-left: 0.25rem;

		background-color: var(--betme-black);
		color: var(--betme-yellow);
		border-radius: 10px;
		border: none;
	}

	.loginform__error {
		color: red;
		font-family: "Montserrat", sans-serif;
	}
</style>
