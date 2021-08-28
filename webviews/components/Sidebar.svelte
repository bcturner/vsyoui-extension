<script lang="ts">

    //TODO : parse list of available platforms from bogus `youi-tv -p fake` call
	let platforms = [
		{ id: 'osx', text: `OSX` },
		{ id: 'tvos', text: `tvOS` },
		{ id: 'ios', text: `iOS` },
		{ id: 'android', text: `Android` },
		{ id: 'win64', text: `Win64` },
		{ id: 'uwp', text: `UWP` },
		{ id: 'linux', text: `Linux` },
		{ id: 'tizen-nacl', text: `Tizen-NaCl` },
		{ id: 'ps4', text: `Playstation 4` }
	];

    let buildArguments: Array<{argumentText: string}> = [];
    let argumentText = '';

    //Load default Debug Config
	let selectedPlatform = platforms[0];
    let target = '';
    let config = 'debug';
    let buildConfig = {platform: selectedPlatform.id, target: target, config: config, arguments: buildArguments};

    function onConfigChange(event: { currentTarget: { value: string; }; }) {
		config = event.currentTarget.value;
        updateConfig();
	}

    function updateConfig() {
		buildConfig.platform = selectedPlatform.id;
        buildConfig.target = target;
        buildConfig.config = config;
        buildConfig.arguments = buildArguments;
	}
</script>


<h1>VSYoui CLI</h1>
<h3>Platform</h3>

<!-- Platform dropdown -->
<select bind:value={selectedPlatform} on:change={updateConfig}>
    {#each platforms as platform}
        <option value={platform}>
            {platform.text}
        </option>
    {/each}
</select>

<!-- Platform dropdown -->
<h3>Config</h3>
<label>
	<input checked={config==='debug'} on:change={onConfigChange} type="checkbox" name="config" value="debug" />
    Debug
</label>
<label>
	<input checked={config==='release'} on:change={onConfigChange} type="checkbox" name="config" value="release" />
    Release
</label>

<!-- Build Arguments List -->
<h3>Arguments</h3>
<form 
    on:submit|preventDefault={(e) => {
        buildArguments = [{argumentText}, ...buildArguments];
        argumentText = '';
        updateConfig();
    }}>
    <input bind:value={argumentText}>
</form>

<ul on:change={updateConfig}>
    {#each buildArguments as arg (arg.argumentText)}
        <li on:click={() => {
            let index = buildArguments.indexOf(arg);
            let argumentsCopy = buildArguments;
            if (index > -1) {
                argumentsCopy.splice(index, 1);
                buildArguments = argumentsCopy;
            }
        }}>{arg.argumentText}</li>
    {/each}
</ul>

<!-- Build Target Input -->
<h3>Target</h3>
<input bind:value={target} on:change={updateConfig}/>

<!-- Generate Button -->
<button on:click={() => {
    tsvscode.postMessage({
        type: 'onGenerate',
        value: buildConfig
    });
}}>Generate</button>

<!-- Build Button -->
<button on:click={() => {
    tsvscode.postMessage({
        type: 'onBuild',
        value: buildConfig
    });
}}>Build</button>