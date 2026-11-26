
<script>
// @ts-nocheck

    const personajes = [
        { nombre: "plimplim", color: "bg-sky-300" },
        { nombre: "cangrejo", color: "bg-orange-300" },
        { nombre: "nesho", color: "bg-pink-300" },
        { nombre: "pam", color: "bg-green-500" },
        { nombre: "wichiwi", color: "bg-purple-300" },
        { nombre: "acuarela", color: "bg-green-300" },
        { nombre: "meili", color: "bg-red-600" },
        { nombre: "hoggie", color: "bg-blue-600" },
    ];
    import { onMount } from "svelte";
    let personajesMezclados = $state([...personajes]);

    let mostrarModal = $state(false);

    let personajeObjetivo = $state(
        personajes[Math.floor(Math.random() * personajes.length)]
    );

    let resultado = $state("");
    let aciertos = $state(0);

    let audioAplausos;
let audioVitores;

function reproducirCelebracion() {
    if (audioAplausos ) {
        audioAplausos.currentTime = 0;
        

        audioAplausos.volume = 0.8;
        

        audioAplausos.play();
        setTimeout(() => {
            audioAplausos.pause();
            audioAplausos.currentTime = 0;
            
        }, 3000);
        
    }
}

    // Seleccionar siguiente personaje
    function siguientePersonaje() {
    let disponibles = personajes.filter(
        personaje => personaje.nombre !== personajeObjetivo.nombre
    );

    personajeObjetivo =
        disponibles[Math.floor(Math.random() * disponibles.length)];

    mezclarPersonajes();

    resultado = "";
}

    // Comprobar selección
    function seleccionarPersonaje(personaje) {
    if (mostrarModal) return;

    if (personaje.nombre === personajeObjetivo.nombre) {
        resultado = "correcto";
        aciertos++;
        mostrarModal = true;

        reproducirCelebracion();

        setTimeout(() => {
            mostrarModal = false;
            siguientePersonaje();
            escucharPregunta();
        }, 3500);

    } else {
        resultado = "incorrecto";
        mostrarModal = true;

        setTimeout(() => {
            mostrarModal = false;
            resultado = "";
        }, 1300);
    }
}

    // Reproducir pregunta
    function escucharPregunta() {
    if (typeof window === "undefined") return;

    window.speechSynthesis.cancel();

    setTimeout(() => {
        const pregunta = new SpeechSynthesisUtterance(
            `¿Dónde está ${personajeObjetivo.nombre}?`
        );

        // Voz en español
        pregunta.lang = "es-ES";

        // Tono alegre y agudo
        pregunta.rate = 0.67;
        pregunta.pitch = 6.5;
        pregunta.volume = 1;

        // Buscar voz femenina
        const voces = window.speechSynthesis.getVoices();

        const vozFemenina = voces.find(voz =>
            voz.lang.toLowerCase().startsWith("es") &&
            /female|femenina|helena|laura|monica|lucia/i.test(voz.name)
        );

        if (vozFemenina) {
            pregunta.voice = vozFemenina;
        }

        window.speechSynthesis.speak(pregunta);

    }, 1200);
}

    function mezclarPersonajes() {
    personajesMezclados = [...personajes].sort(
        () => Math.random() - 0.5
    );
}
    onMount(() => {
        const temporizador = setTimeout(() => {
            escucharPregunta();
        }, 700);

        return () => clearTimeout(temporizador);
    });
</script>

<svelte:head>
    <title>Encuentra los personajes | Plim Plim</title>
</svelte:head>

<main class="h-full w-full box-border overflow-hidden bg-gradient-to-b from-sky-200 via-white to-blue-300 p-3">
    <section class="mx-auto flex h-auto max-w-5xl flex-col gap-2">

        <!-- PREGUNTA -->
        <div class="shrink-0 rounded-3xl bg-gray-200 p-3 text-center shadow-lg">

            <p class="mb-1 text-xs font-bold uppercase tracking-widest text-sky-500">
                ¡Vamos a jugar!
            </p>

            <h1 class="text-xl  font-black text-sky-800">
                ¿Dónde está
                <span class="text-orange-500 capitalize">
                    {personajeObjetivo.nombre}
                </span> ?
            </h1>

            <button
                onclick={escucharPregunta}
                class="mt-2 rounded-full bg-sky-500 px-6 py-2
                text-xl font-bold text-white shadow-md transition
                hover:bg-sky-600 active:scale-95"
            >
                🔊 
            </button>
        </div>

        <!-- GRID DE PERSONAJES -->
        <div class="grid flex-1  min-h-0 grid-cols-3 grid-rows-2 gap-2">

            {#each personajesMezclados as personaje (personaje.nombre)}

                <button
                    onclick={() => seleccionarPersonaje(personaje)}
                    disabled={mostrarModal}
                    class="group flex min-h-0 min-w-0 flex-col items-center
                    justify-center overflow-hidden rounded-2xl
                    border-4 border-white p-2 shadow-lg
                    transition-all duration-300
                    hover:scale-[1.02] hover:shadow-xl active:scale-95
                    disabled:cursor-default
                    {personaje.color}"
                >

                        <div class="flex items-center justify-center">

                            <img
                                src={`/img/plim/${personaje.nombre}.png`}
                                alt={personaje.nombre}
                                class="max-h-full h-24 max-w-full object-contain
                                transition-transform duration-300
                                group-hover:scale-110"
                            />

                        </div>

                    <span class="mt-1 shrink-0 rounded-full bg-white/90 px-3 py-1
                        text-xs font-black capitalize text-gray-800 shadow-sm">
                        {personaje.nombre}
                    </span>

                </button>

            {/each}

        </div>

    </section>

    <!-- MODAL -->
    {#if mostrarModal}

        <div
            class="fixed inset-0 z-50 flex items-center justify-center
            bg-sky-950/60 p-5 backdrop-blur-sm"
        >

            {#if resultado === "correcto"}

                <div class="w-full max-w-sm rounded-[2rem]
                    border-8 border-yellow-300
                    bg-gradient-to-br from-yellow-100 via-green-100 to-sky-100
                    p-8 text-center shadow-2xl animate-bounce">

                    <div class="mb-3 text-7xl">
                        🎉
                    </div>

                    <h2 class="text-3xl font-black text-green-600">
                        ¡MUY BIEN!
                    </h2>

                    <p class="mt-3 text-lg font-bold text-sky-800">
                        ¡Has encontrado a {personajeObjetivo.nombre}!
                    </p>

                    <div class="mt-5 text-4xl">
                        ⭐ 🥳 ⭐
                    </div>

                </div>

            {:else if resultado === "incorrecto"}

                <div class="w-full max-w-sm rounded-[2rem]
                    border-8 border-orange-300
                    bg-gradient-to-br from-orange-100 via-yellow-100 to-pink-100
                    p-8 text-center shadow-2xl">

                    <div class="mb-3 text-7xl">
                        🤗
                    </div>

                    <h2 class="text-3xl font-black text-orange-600">
                        ¡CASI!
                    </h2>

                    <p class="mt-3 text-lg font-bold text-pink-700">
                        ¡Inténtalo otra vez! Tú puedes.
                    </p>

                    <div class="mt-5 text-4xl">
                        💪 🌈 ❤️
                    </div>

                </div>

            {/if}

        </div>

    {/if}
    <audio
    bind:this={audioAplausos}
    src="/audio/aplauso.mp3"
    preload="auto"
></audio>
</main>



