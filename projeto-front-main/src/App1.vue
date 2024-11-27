<template>
    <html lang="pt-br">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Kanban's</title>
        <link rel="stylesheet" href="styles.css">
        <link rel="icon" type="image/png" href="projeto-front-main\src\Logo.png">
    </head>
    <body>
    <!-- Barra lateral esquerda com controles -->
        <div class="sidebar">
            <button id="newBoardBtn" class="sidebar-btn">+</button>
            <button id="folderBtn" class="sidebar-btn">📁</button>
            <!-- Folder content will be dynamically populated -->
            <div id="savedBoards" class="saved-boards hidden"></div>
        </div>

        <!-- Área principal do quadro Kanban -->
        <div class="main-content">
            <div id="kanbanBoard" class="kanban-board">
                <div class="column" id="todo">
                    <h2>TO DO</h2>
                    <div class="dropzone" data-column="todo"></div>
                </div>
                <div class="column" id="doing">
                    <h2>DOING</h2>
                    <div class="dropzone" data-column="doing"></div>
                </div>
                <div class="column" id="done">
                    <h2>DONE</h2>
                    <div class="dropzone" data-column="done"></div>
                </div>
            </div>

            <!-- Paleta dos Post-its -->
            <div class="sticky-notes-palette">
                <div class="sticky-note-icon" draggable="true" style="background-color: #F02B15"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #F06622"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #EFCB0E"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #0e8a2d"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #73FF9B"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #73FFE9"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #73B4FF"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #737BFF"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #B473FF"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #FF73B4"></div>
                <div class="sticky-note-icon" draggable="true" style="background-color: #FF73E9"></div>
            </div>
        </div>
    </body>
    </html>
</template>
<script>
    // Armazenar dados dos quadros
    let boards = [];
    let currentBoardIndex = 0;

    // Inicializar a aplicação
    document.addEventListener('DOMContentLoaded', () => {
        // Carregar quadros salvos do localStorage
        loadBoards();
        
        // Inicializar arrastar e soltar
        initializeDragAndDrop();
        
        // Inicializar botões
        initializeButtons();
        
        // Inicializar post-its existentes
        initializeExistingNotes();
    });

    // Carregar quadros do localStorage
    function loadBoards() {
        const savedBoards = localStorage.getItem('kanbanBoards');
        if (savedBoards) {
            boards = JSON.parse(savedBoards);
            if (boards.length === 0) {
                createNewBoard();
            }
        } else {
            createNewBoard();
        }
        renderCurrentBoard();
    }

    // Criar um novo quadro
    function createNewBoard() {
        const newBoard = {
            id: Date.now(),
            name: `Board ${boards.length + 1}`,
            notes: []
        };
        boards.push(newBoard);
        currentBoardIndex = boards.length - 1;
        saveBoards();
    }

    // Salvar quadros no localStorage
    function saveBoards() {
        localStorage.setItem('kanbanBoards', JSON.stringify(boards));
    }

    // Inicializar funcionalidade de arrastar e soltar
    function initializeDragAndDrop() {
        const kanbanBoard = document.getElementById('kanbanBoard');
        const stickyNoteIcons = document.querySelectorAll('.sticky-note-icon');

        stickyNoteIcons.forEach(icon => {
            icon.addEventListener('dragstart', handleIconDragStart);
            icon.addEventListener('dragend', handleDragEnd);
        });

        kanbanBoard.addEventListener('dragover', handleDragOver);
        kanbanBoard.addEventListener('drop', handleDrop);
    }

    // Manipuladores de eventos de arrastar e soltar
    function handleIconDragStart(e) {
        e.dataTransfer.setData('text/plain', 'Click to edit');
        e.dataTransfer.setData('color', e.target.style.backgroundColor);
        e.dataTransfer.setData('source', 'palette');
    }

    function handleNoteDragStart(e) {
        e.target.classList.add('dragging');
        e.dataTransfer.setData('text/plain', e.target.querySelector('.note-content').innerHTML);
        e.dataTransfer.setData('color', e.target.style.backgroundColor);
        e.dataTransfer.setData('source', 'board');
    }

    function handleDragEnd(e) {
        e.target.classList.remove('dragging');
    }

    function handleDragOver(e) {
        e.preventDefault();
    }

    function handleDrop(e) {
        e.preventDefault();
        const content = e.dataTransfer.getData('text/plain');
        const color = e.dataTransfer.getData('color');
        const source = e.dataTransfer.getData('source');

        const dropzone = e.target.closest('.dropzone');
        if (!dropzone) return;

        const rect = dropzone.getBoundingClientRect();

        if (source === 'board') {
            const draggedNote = document.querySelector('.dragging');
            if (draggedNote) {
                draggedNote.style.left = `${e.clientX - rect.left}px`;
                draggedNote.style.top = `${e.clientY - rect.top}px`;
                dropzone.appendChild(draggedNote);
            }
        } else {
            const newNote = createStickyNote(content, color);
            newNote.style.left = `${e.clientX - rect.left}px`;
            newNote.style.top = `${e.clientY - rect.top}px`;
            dropzone.appendChild(newNote);
        }

        updateBoardState();
    }

    // Criar um novo elemento de post-it
    function createStickyNote(content, color) {
        const note = document.createElement('div');
        note.className = 'sticky-note';
        note.draggable = true;
        note.style.backgroundColor = color;
        note.style.position = 'absolute';
        
        const noteContent = document.createElement('div');
        noteContent.className = 'note-content';
        noteContent.contentEditable = true;
        noteContent.innerHTML = content;
        note.appendChild(noteContent);
        
        note.addEventListener('dragstart', handleNoteDragStart);
        note.addEventListener('dragend', handleDragEnd);
        note.addEventListener('contextmenu', handleContextMenu);
        
        let isDragging = false;
        let startX, startY;

        note.addEventListener('mousedown', (e) => {
            isDragging = true;
            startX = e.clientX - note.offsetLeft;
            startY = e.clientY - note.offsetTop;
        });

        document.addEventListener('mousemove', (e) => {
            if (isDragging) {
                note.style.left = `${e.clientX - startX}px`;
                note.style.top = `${e.clientY - startY}px`;
            }
        });

        document.addEventListener('mouseup', () => {
            if (isDragging) {
                isDragging = false;
                updateBoardState();
            }
        });
        
        return note;
    }

    // Manipular menu de contexto
    function handleContextMenu(e) {
        e.preventDefault();
        const note = e.target.closest('.sticky-note');
        if (note) {
            const menu = createContextMenu(note);
            document.body.appendChild(menu);
            
            menu.style.top = `${e.clientY}px`;
            menu.style.left = `${e.clientX}px`;
            
            document.addEventListener('click', () => menu.remove(), { once: true });
        }
    }

    // Criar menu de contexto
    function createContextMenu(note) {
        const menu = document.createElement('div');
        menu.className = 'context-menu';
        
        const deleteOption = document.createElement('div');
        deleteOption.className = 'context-menu-option';
        deleteOption.textContent = 'Deletar';
        deleteOption.addEventListener('click', () => {
            note.remove();
            updateBoardState();
        });
        
        menu.appendChild(deleteOption);
        return menu;
    }

    // Inicializar botões
    function initializeButtons() {
        const newBoardBtn = document.getElementById('newBoardBtn');
        const folderBtn = document.getElementById('folderBtn');
        const savedBoards = document.getElementById('savedBoards');

        newBoardBtn.addEventListener('click', () => {
            createNewBoard();
            renderCurrentBoard();
        });

        folderBtn.addEventListener('click', () => {
            savedBoards.classList.toggle('hidden');
            renderSavedBoards();
        });
    }

    // Renderizar o quadro atual
    function renderCurrentBoard() {
        const board = boards[currentBoardIndex];
        if (!board) return;

        document.querySelectorAll('.dropzone').forEach(zone => {
            zone.innerHTML = '';
        });

        if (board.notes) {
            board.notes.forEach(note => {
                const noteElement = createStickyNote(note.content, note.color);
                noteElement.style.left = `${note.x}px`;
                noteElement.style.top = `${note.y}px`;
                const column = document.querySelector(`[data-column="${note.column}"]`);
                if (column) {
                    column.appendChild(noteElement);
                }
            });
        }

        initializeExistingNotes();
    }

    // Renderizar quadros salvos na pasta
    function renderSavedBoards() {
        const savedBoardsContainer = document.getElementById('savedBoards');
        savedBoardsContainer.innerHTML = '';

        boards.forEach((board, index) => {
            const boardItem = document.createElement('div');
            boardItem.className = 'saved-board-item';
            boardItem.textContent = board.name;
            boardItem.addEventListener('click', () => {
                currentBoardIndex = index;
                renderCurrentBoard();
                savedBoardsContainer.classList.add('hidden');
            });
            boardItem.addEventListener('contextmenu', (e) => handleBoardContextMenu(e, index));
            savedBoardsContainer.appendChild(boardItem);
        });
    }

    // Manipular menu de contexto para boards salvos
    function handleBoardContextMenu(e, boardIndex) {
        e.preventDefault();
        const menu = createBoardContextMenu(boardIndex);
        document.body.appendChild(menu);
        
        menu.style.top = `${e.clientY}px`;
        menu.style.left = `${e.clientX}px`;
        
        document.addEventListener('click', () => menu.remove(), { once: true });
    }

    // Criar menu de contexto para boards salvos
    function createBoardContextMenu(boardIndex) {
        const menu = document.createElement('div');
        menu.className = 'context-menu';
        
        const deleteOption = document.createElement('div');
        deleteOption.className = 'context-menu-option';
        deleteOption.textContent = 'Deletar';
        deleteOption.addEventListener('click', () => {
            if (confirm('Você tem certeza que quer deletar esse Kanban?')) {
                deleteBoard(boardIndex);
            }
        });
        
        menu.appendChild(deleteOption);
        return menu;
    }

    // Deletar um board salvo
    function deleteBoard(index) {
        boards.splice(index, 1);
        if (boards.length === 0) {
            createNewBoard();
        } else if (currentBoardIndex >= index) {
            currentBoardIndex = Math.max(0, currentBoardIndex - 1);
        }
        saveBoards();
        renderSavedBoards();
        renderCurrentBoard();
    }

    // Atualizar estado do quadro quando ocorrem mudanças
    function updateBoardState() {
        const board = boards[currentBoardIndex];
        board.notes = [];

        document.querySelectorAll('.sticky-note').forEach(note => {
            const column = note.closest('.dropzone').dataset.column;
            const rect = note.getBoundingClientRect();
            const dropzone = note.closest('.dropzone');
            const dropzoneRect = dropzone.getBoundingClientRect();

            board.notes.push({
                content: note.querySelector('.note-content').innerHTML,
                color: note.style.backgroundColor,
                x: rect.left - dropzoneRect.left,
                y: rect.top - dropzoneRect.top,
                column: column
            });
        });

        saveBoards();
    }

    // Inicializar post-its existentes
    function initializeExistingNotes() {
        document.querySelectorAll('.sticky-note').forEach(note => {
            note.draggable = true;
            note.addEventListener('dragstart', handleNoteDragStart);
            note.addEventListener('dragend', handleDragEnd);
            note.addEventListener('contextmenu', handleContextMenu);
            
            const noteContent = note.querySelector('.note-content');
            if (!noteContent) {
                const newNoteContent = document.createElement('div');
                newNoteContent.className = 'note-content';
                newNoteContent.contentEditable = true;
                newNoteContent.innerHTML = note.innerHTML;
                note.innerHTML = '';
                note.appendChild(newNoteContent);
            }

            noteContent.addEventListener('input', updateBoardState);

            let isDragging = false;
            let startX, startY;

            note.addEventListener('mousedown', (e) => {
                isDragging = true;
                const rect = note.getBoundingClientRect();
                startX = e.clientX - rect.left;
                startY = e.clientY - rect.top;
            });

            document.addEventListener('mousemove', (e) => {
                if (isDragging) {
                    const dropzone = note.closest('.dropzone');
                    const rect = dropzone.getBoundingClientRect();
                    note.style.left = `${e.clientX - rect.left - startX}px`;
                    note.style.top = `${e.clientY - rect.top - startY}px`;
                }
            });

            document.addEventListener('mouseup', () => {
                if (isDragging) {
                    isDragging = false;
                    updateBoardState();
                }
            });
        });
    }
</script>

<style>
    *{
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    body {
        display: flex;
        min-height: 100vh;
        font-family: Arial, sans-serif;
        background-color: #f0f0f0;
    }

    /* Barra lateral */
    .sidebar {
        width: 60px;
        background-color: #333;
        padding: 20px 10px;
        display: flex;
        flex-direction: column;
        gap: 20px;
        position: relative;
        right: 10%;
        top: 19%;
    }

    .sidebar-btn {
        width: 40px;
        height: 40px;
        border: none;
        border-radius: 8px;
        background-color: #444;
        color: white;
        cursor: pointer;
        font-size: 20px;
        transition: background-color 0.3s;
    }

    .sidebar-btn:hover {
        background-color: #555;
    }

    .saved-boards {
        position: absolute;
        left: 60px;
        top: 60px;
        background-color: #444;
        border-radius: 8px;
        padding: 10px;
        width: 200px;
        z-index: 100;
    }

    .saved-boards.hidden {
        display: none;
    }

    .saved-board-item {
        color: white;
        padding: 8px;
        cursor: pointer;
        border-radius: 4px;
        user-select: none; /* Impede a seleção de texto */
    }

    .saved-board-item:hover {
        background-color: #555;
    }

    /* Área do conteúdo principal */
    .main-content {
        flex-grow: 1;
        padding: 20px;
        display: flex;
        flex-direction: column;
        gap: 20px;
    }

    /* Quadro Kanban */
    .kanban-board {
        display: flex;
        gap: 20px;
        flex-grow: 1;
        background-color: #000;
        padding: 20px;
        border: 8px solid #B8860B;
        border-radius: 8px;
        position: relative;
        min-height: 600px;
    }

    .column {
        flex: 1;
        min-width: 300px;
        display: flex;
        flex-direction: column;
        gap: 10px;
    }

    .column h2 {
        color: white;
        text-align: center;
        padding: 10px;
        background-color: rgba(255, 255, 255, 0.1);
        border-radius: 4px;
    }

    .dropzone {
        flex-grow: 1;
        padding: 10px;
        min-height: 100px;
        border: 2px dashed rgba(255, 255, 255, 0.2);
        border-radius: 4px;
        transition: background-color 0.3s;
        position: relative;
    }

    /* Paleta dos Post-its */
    .sticky-notes-palette {
        display: flex;
        justify-content: center;
        gap: 10px;
        padding: 20px;
        background-color: #222;
        border-radius: 8px;
        overflow-x: auto;
        white-space: nowrap;
    }

    .sticky-note-icon {
        width: 40px;
        height: 40px;
        border-radius: 4px;
        cursor: move;
        transition: transform 0.3s;
    }

    .sticky-note-icon:hover {
        transform: scale(1.1);
    }

    .sticky-note {
        width: 120px;
        height: 120px;
        padding: 5px;
        cursor: move;
        border-radius: 4px;
        box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
        display: flex;
        align-items: center;
        justify-content: center;
        text-align: center;
        font-size: 12px;
        transition: transform 0.3s;
        position: absolute;
        z-index: 10;
    }

    .sticky-note:hover {
        transform: scale(1.05);
    }

    .sticky-note.dragging {
        opacity: 0.5;
    }

    .note-content {
        width: 100%;
        height: 100%;
        overflow: auto;
        outline: none;
        display: flex;
        align-items: center;
        justify-content: center;
        text-align: center;
    }

    /* Estilos de conteúdo editáveis ​​*/
    [contenteditable="true"] {
        outline: none;
        cursor: text;
    }

    [contenteditable="true"]:empty:before {
        content: "Clique para editar";
        color: rgba(0, 0, 0, 0.5);
    }

    .delete-note {
        position: absolute;
        top: 2px;
        right: 2px;
        background: none;
        border: none;
        color: rgba(0, 0, 0, 0.5);
        font-size: 14px;
        cursor: pointer;
        padding: 0;
        line-height: 1;
    }

    .delete-note:hover {
        color: rgba(0, 0, 0, 0.8);
    }

    /* Menu do conteúdo */
    .context-menu {
        position: fixed;
        background: white;
        border: 1px solid #ccc;
        box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
        border-radius: 4px;
        z-index: 1000;
    }

    .context-menu-option {
        padding: 8px 12px;
        cursor: pointer;
        color: #000;
    }

    .context-menu-option:hover {
        background-color: #f0f0f0;
    }
</style>
