

<script>
    import { onMount, onDestroy } from "svelte";

    // ==========================================
    // ANIMALES
    // ==========================================

    const animales = [
        {
            id: "gallo",
            nombre: "Bartolito",
            animal: "Gallo",
            imagen: "/img/zenon/bartolito.png",
            sonido: "/audio/zenon/gallo.mp3",
            color: "bg-orange-100"
        },
        {
            id: "cerdo",
            nombre: "Chancha",
            animal: "Cerdo",
            imagen: "/img/zenon/chancha.png",
            sonido: "/audio/zenon/pig.mp3",
            color: "bg-pink-100"
        },
        {
            id: "lobo",
            nombre: "Lobo",
            animal: "Lobo",
            imagen: "/img/zenon/lobo.png",
            sonido: "/audio/zenon/lobo.mp3",
            color: "bg-purple-100"
        },
        {
            id: "pato",
            nombre: "Pato",
            animal: "Pato",
            imagen: "/img/zenon/pato.png",
            sonido: "/audio/zenon/duck.mp3",
            color: "bg-yellow-100"
        },
        {
            id: "caballo",
            nombre: "Percherón",
            animal: "Caballo",
            imagen: "/img/zenon/percheron.png",
            sonido: "/audio/zenon/horse.mp3",
            color: "bg-blue-100"
        },
        {
            id: "vaca",
            nombre: "Vacalola",
            animal: "Vaca",
            imagen: "/img/zenon/vacalola.png",
            sonido: "/audio/zenon/cow.mp3",
            color: "bg-green-100"
        }
    ];

    // ==========================================
    // ESTADOS
    // ==========================================

    let objetivo = $state(null);
    let opciones = $state([]);

    let estado = $state("jugando");
    let aciertos = $state(0);
    let mensaje = $state("");

    let audioActual = null;
    let audioAplausos;
    let audioVitores;

    let temporizador = null;
    let temporizadorCelebracion = null;
    let temporizadorVoz = null;

    let ultimoId = null;

    // ==========================================
    // MEZCLAR ANIMALES
    // ==========================================

    function mezclar(array) {
        const copia = [...array];

        for (let i = copia.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));

            [copia[i], copia[j]] = [copia[j], copia[i]];
        }

        return copia;
    }

    // ==========================================
    // DETENER AUDIO
    // ==========================================

    function detenerSonido() {
        if (audioActual) {
            audioActual.pause();
            audioActual.currentTime = 0;
            audioActual = null;
        }
    }

    // ==========================================
    // REPRODUCIR SONIDO DEL ANIMAL
    // ==========================================

    function reproducirSonido() {
        if (!objetivo) return;

        detenerSonido();

        audioActual = new Audio(objetivo.sonido);
        audioActual.volume = 1;

        audioActual.play().catch(error => {
            console.error("Error reproduciendo el sonido:", error);
        });
    }

    // ==========================================
    // VOZ FEMENINA
    // ==========================================

    function hablar(texto, alTerminar = null) {
        if (typeof window === "undefined") return;

        window.speechSynthesis.cancel();

        const voz = new SpeechSynthesisUtterance(texto);

        voz.lang = "es-ES";
        voz.rate = 0.85;
        voz.pitch = 1.2;
        voz.volume = 1;

        const voces = window.speechSynthesis.getVoices();

        const vozFemenina = voces.find(v =>
            v.lang.toLowerCase().startsWith("es") &&
            /female|femenina|helena|laura|monica|lucia/i.test(v.name)
        );

        if (vozFemenina) {
            voz.voice = vozFemenina;
        }

        if (alTerminar) {
            voz.onend = alTerminar;
        }

        window.speechSynthesis.speak(voz);
    }

    // ==========================================
    // ESCUCHAR PREGUNTA
    // ==========================================

    function escucharPregunta() {
        if (!objetivo) return;

        hablar(
            "¿Qué animal hace este sonido?",
            reproducirSonido
        );
    }

    // ==========================================
    // NUEVA RONDA
    // ==========================================

    function nuevaRonda() {
        detenerSonido();

        if (temporizador) {
            clearTimeout(temporizador);
        }

        if (temporizadorVoz) {
            clearTimeout(temporizadorVoz);
        }

        const candidatos = animales.filter(
            animal => animal.id !== ultimoId
        );

        objetivo = candidatos[
            Math.floor(Math.random() * candidatos.length)
        ];

        ultimoId = objetivo.id;

        opciones = mezclar(animales);

        estado = "jugando";
        mensaje = "";

        temporizadorVoz = setTimeout(() => {
            escucharPregunta();
        }, 600);
    }

    // ==========================================
    // CELEBRACIÓN
    // ==========================================

    function reproducirCelebracion() {
        if (!audioAplausos || !audioVitores) return;

        if (temporizadorCelebracion) {
            clearTimeout(temporizadorCelebracion);
        }

        audioAplausos.currentTime = 0;
        audioVitores.currentTime = 0;

        audioAplausos.volume = 0.8;
        audioVitores.volume = 0.7;

        audioAplausos.play().catch(() => {});
        audioVitores.play().catch(() => {});

        temporizadorCelebracion = setTimeout(() => {
            audioAplausos.pause();
            audioVitores.pause();

            audioAplausos.currentTime = 0;
            audioVitores.currentTime = 0;
        }, 2500);
    }

    // ==========================================
    // SELECCIONAR ANIMAL
    // ==========================================

    function seleccionarAnimal(animal) {
        if (estado !== "jugando") return;

        detenerSonido();

        if (animal.id === objetivo.id) {

            // RESPUESTA CORRECTA

            estado = "correcto";

            mensaje = `¡Has encontrado a ${objetivo.nombre}!`;

            aciertos++;

            reproducirCelebracion();

            hablar(
                `¡Muy bien! ¡Es el ${objetivo.animal}!`
            );

            temporizador = setTimeout(() => {
                nuevaRonda();
            }, 3000);

        } else {

            // RESPUESTA INCORRECTA

            estado = "incorrecto";

            mensaje = "¡Inténtalo otra vez! ¡Tú puedes!";

            hablar("¡Casi! ¡Inténtalo otra vez! Tú puedes.");

            temporizador = setTimeout(() => {
                estado = "jugando";
                mensaje = "";
            }, 1400);
        }
    }

    // ==========================================
    // INICIO AUTOMÁTICO
    // ==========================================

    onMount(() => {
        const inicio = setTimeout(() => {
            nuevaRonda();
        }, 700);

        return () => clearTimeout(inicio);
    });

    // ==========================================
    // LIMPIEZA
    // ==========================================

    onDestroy(() => {
        if (temporizador) clearTimeout(temporizador);

        if (temporizadorCelebracion) {
            clearTimeout(temporizadorCelebracion);
        }

        if (temporizadorVoz) {
            clearTimeout(temporizadorVoz);
        }

        detenerSonido();

        if (typeof window !== "undefined") {
            window.speechSynthesis.cancel();
        }
    });
</script>

<!-- AUDIOS DE CELEBRACIÓN -->

<audio
    bind:this={audioAplausos}
    src="/audio/aplausos.mp3"
    preload="auto"
></audio>

<audio
    bind:this={audioVitores}
    src="/audio/vitores.mp3"
    preload="auto"
></audio>


<main class="h-[610px] w-full box-border overflow-hidden bg-gradient-to-b from-sky-100 via-white to-purple-50 p-3">

    <section class="mx-auto flex h-full max-w-5xl flex-col gap-3">

        <!-- CABECERA -->

        <header class="flex shrink-0 items-center justify-between rounded-3xl bg-white p-4 shadow-md">

            <div>
                <h1 class="text-xl font-black text-purple-600 sm:text-3xl">
                    ¡Adivina el animal!
                </h1>

                <p class="text-sm font-bold text-gray-500">
                    Escucha y encuentra al animal
                </p>
            </div>

            <div class="rounded-2xl bg-yellow-100 px-4 py-2 text-center">

                <span class="text-xs font-bold text-yellow-700">
                    ACIERTOS
                </span>

                <p class="text-2xl font-black text-yellow-600">
                    {aciertos}
                </p>

            </div>

        </header>


        <!-- PREGUNTA -->

        <div class="flex shrink-0 flex-col items-center gap-2 rounded-3xl bg-white p-3 shadow-md">

            <h2 class="text-center text-lg font-black text-gray-700 sm:text-2xl">
                ¿Qué animal hace este sonido?
            </h2>

            <button
                onclick={reproducirSonido}
                disabled={!objetivo || estado !== "jugando"}
                class="flex items-center gap-3 rounded-full bg-purple-500 px-8 py-2 text-lg font-black text-white shadow-lg transition hover:scale-105 hover:bg-purple-600 active:scale-95 disabled:opacity-50"
            >
                <span class="text-2xl">🔊</span>
                ESCUCHAR SONIDO
            </button>

        </div>


        <!-- PERSONAJES -->

        <div class="grid min-h-0 flex-1 grid-cols-2 grid-rows-3 gap-3 sm:grid-cols-3 sm:grid-rows-2 sm:gap-5">

            {#each opciones as animal (animal.id)}

                <button
                    onclick={() => seleccionarAnimal(animal)}
                    disabled={estado !== "jugando"}
                    class={`group flex min-h-0 flex-col items-center justify-center overflow-hidden rounded-3xl border-4 border-white p-2 shadow-md transition-all duration-300 hover:-translate-y-1 hover:shadow-xl active:scale-95 disabled:cursor-default
                    ${animal.color}
                    ${estado === "jugando" ? "hover:border-purple-300" : ""}
                    `}
                >

                    <img
                        src={animal.imagen}
                        alt={animal.nombre}
                        class="h-full max-h-32 w-full object-contain transition-transform duration-300 group-hover:scale-110 sm:max-h-40"
                    />

                </button>

            {/each}

        </div>

    </section>


    <!-- MODAL DE ACIERTO -->

    {#if estado === "correcto"}

        <div class="absolute inset-0 z-50 flex items-center justify-center bg-black/50 p-4">

            <div class="w-full max-w-md animate-bounce rounded-[2rem] border-8 border-yellow-300 bg-white p-8 text-center shadow-2xl">

                <div class="mb-3 text-7xl">
                    🎉
                </div>

                <h2 class="text-4xl font-black text-green-500">
                    ¡MUY BIEN!
                </h2>

                <p class="mt-3 text-xl font-bold text-gray-600">
                    {mensaje}
                </p>

                <div class="mt-5 flex justify-center gap-3 text-4xl">
                    ⭐ 🌟 ⭐
                </div>

                <p class="mt-4 text-sm font-bold text-purple-500">
                    ¡Prepárate para el siguiente animal!
                </p>

            </div>

        </div>

    {/if}


    <!-- MODAL DE ERROR -->

    {#if estado === "incorrecto"}

        <div class="absolute inset-0 z-40 flex items-center justify-center bg-black/30 p-4">

            <div class="w-full max-w-sm rounded-[2rem] border-8 border-orange-200 bg-white p-8 text-center shadow-2xl">

                <div class="mb-3 text-7xl">
                    🤗
                </div>

                <h2 class="text-4xl font-black text-orange-500">
                    ¡CASI!
                </h2>

                <p class="mt-3 text-xl font-bold text-gray-600">
                    {mensaje}
                </p>

                <div class="mt-4 text-4xl">
                    💪 🧡
                </div>

            </div>

        </div>

    {/if}

</main>

