# Ta-te-ti-ATR
Versión del ta-te-ti creada en el CPEM N°80 de Chos Malal. Idea original de llevar a código: Miguel Esteban Manoukian
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ta-te-ti ATR</title>
    <style>
        body {
            background-color: darkgray;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            height: 100vh;
        }

        #mensaje {
            position: relative;
            font-size: x-large;
        }

        #t0, #t1, #t2, #t3, #t4, #t5, #t6, #t7, #t8 {
            width: 100%;
            height: 100%;
            background-color: rgb(255, 255, 255);
            display: flex;
            flex-wrap: wrap;
            border: 1px solid black;
        }

        #tableroPpal {
            display: grid;
            grid-template-columns: repeat(3, 300px);
            grid-template-rows: repeat(3, 300px);
            gap: 10px;
            width: 900px;
            height: 900px;
        }

        .active-board {
            background-color: blueviolet;
        }

        .active-board .casilla {
            border: 4px solid black;
        }

        .casilla {
            width: 100px;
            height: 100px;
            background-color: white;
            border: solid 2px black;
            box-sizing: border-box;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .círculo {
            height: 90px;
            width: 90px;
            border-radius: 50%;
            border: 15px solid green;
            box-sizing: border-box;
        }

        .cruz {
            height: 90px;
            width: 90px;
            position: relative;
            transform: rotate(45deg);
        }

        .cruz:before, .cruz:after {
            content: "";
            position: absolute;
            background-color: darkred;
        }

        .cruz:before {
            left: 50%;
            width: 30%;
            margin-left: -15%;
            height: 100%;
        }

        .cruz:after {
            top: 50%;
            height: 30%;
            margin-top: -15%;
            width: 100%;
        }

        .cruzGanadora {
            width: 300px;
            height: 300px;
            position: relative;
        }

        .cruzGanadora:before, .cruzGanadora:after {
            content: '';
            position: absolute;
            background-color: darkred;
            width: 30px;
            height: 300px;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) rotate(45deg);
        }

        .cruzGanadora:before {
            transform: translate(-50%, -50%) rotate(-45deg);
        }

        .círculoGanador {
            width: 300px;
            height: 300px;
            background-color: green;
            border-radius: 50%;
            display: flex;
            position: center;
            align-items: center;
            justify-content: center;
        }
    </style>
</head>
<body>
    <div id="tableroPpal">
        <div id="t0" class="tablero"></div>
        <div id="t1" class="tablero"></div>
        <div id="t2" class="tablero"></div>
        <div id="t3" class="tablero"></div>
        <div id="t4" class="tablero"></div>
        <div id="t5" class="tablero"></div>
        <div id="t6" class="tablero"></div>
        <div id="t7" class="tablero"></div>
        <div id="t8" class="tablero"></div>
    </div>
    <p id="mensaje"></p>

    <script>
        const tableroPpal = document.querySelector("#tableroPpal");
        const mensaje = document.querySelector("#mensaje");

        const celdas = ["", "", "", "", "", "", "", "", ""];
        let marca = "cruz";
        mensaje.textContent = "Cruz empieza";

        function crearTablero(targetId) {
            const target = document.querySelector(`#${targetId}`);
            celdas.forEach((_celda, index) => {
                const celdaElemento = document.createElement("div");
                celdaElemento.classList.add("casilla");
                celdaElemento.id = index;
                celdaElemento.addEventListener("click", agregaMarca);
                target.appendChild(celdaElemento);
            });
        }

        function crearTableros() {
            for (let i = 0; i <= 8; i++) {
                crearTablero(`t${i}`);
            }
        }

        crearTableros();

        function agregaMarca(e) {
            const marcaForma = document.createElement("div");
            marcaForma.classList.add(marca);
            e.target.append(marcaForma);

            const cellId = parseInt(e.target.id);

            const boardId = cellId;

            const allBoards = document.querySelectorAll(".tablero");
            allBoards.forEach((board) => board.classList.remove("active-board"));

            const activeBoard = document.querySelector("#t" + boardId);
            activeBoard.classList.add("active-board");

            marca = marca === "círculo" ? "cruz" : "círculo";
            mensaje.textContent = "Es ahora, " + marca + " juega.";
            e.target.removeEventListener("click", agregaMarca);
            chequearPuntaje();
        }

        const cruzGanados = [];
        const circuloGanados = [];

        function buscarGanadorGlobal() {
            const tablerosGanadores = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8],
                [0, 3, 6], [1, 4, 7], [2, 5, 8],
                [0, 4, 8], [2, 4, 6]
            ];

            const cruzGanaGlobalmente = tablerosGanadores.some(combinacion => combinacion.every(boardNum => cruzGanados.includes(boardNum)));
            const circuloGanaGlobalmente = tablerosGanadores.some(combinacion => combinacion.every(boardNum => circuloGanados.includes(boardNum)));

            if (cruzGanaGlobalmente) {
                mensaje.textContent = "¡Cruz gana globalmente!";
            } else if (circuloGanaGlobalmente) {
                mensaje.textContent = "¡Círculo gana globalmente!";
            }
        }

        function chequearPuntaje() {
            const tableroGanador = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8],
                [0, 3, 6], [1, 4, 7], [2, 5, 8],
                [0, 4, 8], [2, 4, 6]
            ];

            for (let i = 0; i <= 8; i++) {
                const casillasTablero = document.querySelectorAll(`#t${i} .casilla`);
                for (const combinacion of tableroGanador) {
                    const cruzGana = combinacion.every(
                        (celda) => casillasTablero[celda]?.firstChild?.classList.contains("cruz")
                    );
                    const circuloGana = combinacion.every(
                        (celda) =>
                            casillasTablero[celda]?.firstChild?.classList.contains("círculo")
                    );

                    if (cruzGana) {
                        mensaje.textContent = `¡Cruz gana en el tablero ${i}!`;
                        casillasTablero.forEach((casilla) =>
                            casilla.removeEventListener("click", agregaMarca)
                        );
                        cruzGanados.push(i);
                        buscarGanadorGlobal();
                        reiniciarTablero(i, "cruz");
                        return;
                    }

                    if (circuloGana) {
                        mensaje.textContent = `¡Círculo gana en el tablero ${i}!`;
                        casillasTablero.forEach((casilla) =>
                            casilla.removeEventListener("click", agregaMarca)
                        );
                        circuloGanados.push(i);
                        buscarGanadorGlobal();
                        reiniciarTablero(i, "círculo");
                        return;
                    }
                }
            }
        }

        function reiniciarTablero(targetBoardNum, winner) {
            const targetBoard = document.querySelector(`#t${targetBoardNum}`);
            const shapeElement = document.createElement("div");
            if (winner === "cruz") {
                shapeElement.classList.add("cruzGanadora");
            } else if (winner === "círculo") {
                shapeElement.classList.add("círculoGanador");
            }
            targetBoard.innerHTML = "";
            targetBoard.appendChild(shapeElement);
        }
    </script>
</body>
</html>
