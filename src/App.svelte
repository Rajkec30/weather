<script>
    import {onMount} from 'svelte'

    let loaded = false
    let vrijeme;

    let place = ''
    let lat = ''
    let lon = ''

    const getWeather = ( () => {
        navigator.geolocation.getCurrentPosition(async (position) => {
            const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${position.coords.latitude}&lon=${position.coords.longitude}&appid=cb848c3a3dd3ef2de686991afb87103e`)
            vrijeme = await response.json()
            console.log(vrijeme)
            loaded = true
        })
    })

    const find_search = (async (place) => {
        if (place){
            const response = await fetch(`https://api.locationiq.com/v1/autocomplete?key=pk.238e940315639f0eb8622276b1f1e9c5&q=${place}`)
            let vrijeme2 = await response.json()
            lat = vrijeme2[0].lat
            lon = vrijeme2[0].lon
            const res = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=cb848c3a3dd3ef2de686991afb87103e`)
            vrijeme = await res.json()
            console.log(vrijeme)
            loaded = true
        } else {
            console.log("Wrong place")
        }
    })

    const handleSubmit = () => {
        find_search(place)
        place = ''
    }

    onMount( () => {
        getWeather()
    });

    let now = new Date();
    $: timestamp = now.getDate() + '.' + (now.getMonth()+1) + '.' +now.getFullYear() + '.'
</script>

<main class="bg-[url('./assets/104824.webp')]" style="background-size: cover;background-repeat: no-repeat;">
    <div class="font-['poppins text-[#0070ef] h-screen flex flex-col justify-center items-center">
        {#if !loaded}
            <div class="fixed top-0 left-0 right-0 bottom-0 grid place-items-center z-50">
                <div class="bg-[url('./assets/Rolling-1s-200px.svg')] w-[200px] h-[200px] img bg-no-repeat"></div>
            </div>
        {/if}
        <slot />
        {#if vrijeme}
            <div class="flex flex-col justify-center items-start drop-shadow-2xl rounded-lg md:rounded-3xl bg-white px-5 py-5 md:p-14 w-[90%] md:w-fit h-1/2 md:h-fit md:text-xl gap-4 md:gap-8">
                <form class="text-gray-800 " on:submit|preventDefault={handleSubmit}>
                    <input class="outline-0 focus:ring-2 focus:rounded p-1 border-b-2 focus:border-0" type="text" bind:value={place} placeholder="Grad">
                    <button class="md:ml-5 bg-[#0070ef] text-white px-2 py-1 rounded">Pretrazi</button>
                </form>
                <div class="flex gap-7 texxt-lg md:text-2xl lg:text-4xl">
                    <h1>{vrijeme.name}</h1>
                    <h1 class="text-yellow-400">{(vrijeme.main.temp -273.15).toFixed(1)}C</h1>
                </div>
                <h1 class="">{timestamp}</h1>
                <h1>Oblaci: {vrijeme.weather[0].description}</h1>
                <h1>Vjetar: {Math.round(vrijeme.wind.speed * 1.6)}km/h</h1>
            </div>
        {/if}
    </div>
</main>
