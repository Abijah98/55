<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fancy Interactive Website</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/lucide-react@0.363.0"></script>
    <style>
        /* Custom CSS for button transitions and animations */
        .floating-button {
            position: absolute; /* Allows absolute positioning for floating */
            transition: all 0.3s ease-in-out; /* Smooth transition for position and other properties */
            cursor: pointer;
            /* Add a subtle shadow */
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1), 0 1px 3px rgba(0, 0, 0, 0.08);
        }

        .floating-button:hover {
            transform: scale(1.1); /* Slightly enlarge on hover */
            box-shadow: 0 10px 15px rgba(0, 0, 0, 0.2), 0 4px 6px rgba(0, 0, 0, 0.1); /* Enhance shadow on hover */
        }

        /* Keyframes for the jump animation */
        @keyframes jump {
            0% { transform: translateY(0); }
            50% { transform: translateY(-10px); } /* Jump up */
            100% { transform: translateY(0); } /* Fall back down */
        }

        .jump-animation {
            animation: jump 0.4s ease-in-out; /* Apply the jump animation */
        }

        /* Ensure the body takes full viewport height */
        body {
            min-height: 100vh;
            overflow: hidden; /* Hide overflow from floating buttons */
            font-family: 'Inter', sans-serif; /* Use Inter font */
        }

        /* Container for floating buttons */
        .floating-container {
            position: relative;
            width: 100%;
            height: 100vh; /* Take full viewport height */
            overflow: hidden; /* Hide buttons that float outside */
        }
    </style>
</head>
<body class="bg-gradient-to-br from-purple-400 via-pink-500 to-red-500 text-gray-800">

    <div class="floating-container flex items-center justify-center p-8">
        <div class="text-center z-10">
            <h1 class="text-4xl md:text-6xl font-bold text-white mb-4 drop-shadow-lg">Welcome to Our Fancy Site</h1>
            <p class="text-xl md:text-2xl text-white text-opacity-90 mb-8 drop-shadow-md">Explore the interactive elements below!</p>
        </div>

        <div id="buttons-area" class="absolute inset-0">
            </div>
    </div>

    <script>
        const buttonsArea = document.getElementById('buttons-area');
        const buttonLabels = ["About Us", "Services", "Portfolio", "Contact", "Blog"];
        const buttonColors = ["bg-blue-500", "bg-green-500", "bg-yellow-500", "bg-purple-500", "bg-teal-500"];

        // Create and position buttons
        function createFloatingButtons() {
            buttonsArea.innerHTML = ''; // Clear existing buttons
            const containerWidth = buttonsArea.offsetWidth;
            const containerHeight = buttonsArea.offsetHeight;

            buttonLabels.forEach((label, index) => {
                const button = document.createElement('button');
                button.textContent = label;
                button.classList.add(
                    'floating-button',
                    'px-6',
                    'py-3',
                    'rounded-full',
                    'text-white',
                    'font-semibold',
                    buttonColors[index % buttonColors.length], // Cycle through colors
                    'hover:bg-opacity-90', // Slightly transparent on hover
                    'focus:outline-none', // Remove default focus outline
                    'focus:ring-2', // Add a ring on focus
                    'focus:ring-white',
                    'focus:ring-opacity-50'
                );

                // Random initial position
                const randomX = Math.random() * (containerWidth - 150); // Subtract button width estimate
                const randomY = Math.random() * (containerHeight - 80); // Subtract button height estimate
                button.style.left = `${randomX}px`;
                button.style.top = `${randomY}px`;

                // Add click event listener for the jump effect
                button.addEventListener('click', () => {
                    // Remove existing animation class to restart animation
                    button.classList.remove('jump-animation');
                    // Force reflow to restart animation
                    void button.offsetWidth;
                    // Add the animation class back
                    button.classList.add('jump-animation');

                    // Optional: Do something when the button is clicked
                    console.log(`${label} button clicked!`);
                    // You could navigate, open a modal, etc. here
                    // alert(`You clicked the ${label} button!`); // Using alert is discouraged, use a custom message box instead
                });

                buttonsArea.appendChild(button);
            });
        }

        // Function to gently move buttons over time (the "loop" effect)
        function animateButtons() {
            const buttons = buttonsArea.querySelectorAll('.floating-button');
            const containerWidth = buttonsArea.offsetWidth;
            const containerHeight = buttonsArea.offsetHeight;

            buttons.forEach(button => {
                // Get current position
                let currentX = parseFloat(button.style.left);
                let currentY = parseFloat(button.style.top);

                // Calculate a small random movement
                const moveAmount = 5; // Pixels to move per step
                const deltaX = (Math.random() - 0.5) * moveAmount * 2; // Random value between -moveAmount and +moveAmount
                const deltaY = (Math.random() - 0.5) * moveAmount * 2;

                // Calculate new position, keeping within bounds
                let newX = currentX + deltaX;
                let newY = currentY + deltaY;

                // Keep buttons within the container with some padding
                const buttonWidth = button.offsetWidth;
                const buttonHeight = button.offsetHeight;
                newX = Math.max(0, Math.min(newX, containerWidth - buttonWidth));
                newY = Math.max(0, Math.min(newY, containerHeight - buttonHeight));


                button.style.left = `${newX}px`;
                button.style.top = `${newY}px`;
            });

            // Request next frame for smooth animation
            requestAnimationFrame(animateButtons);
        }

        // Initial setup on page load
        window.onload = () => {
            createFloatingButtons();
            animateButtons(); // Start the continuous floating animation
        };

        // Recalculate button positions on window resize
        window.addEventListener('resize', () => {
             // Recreate buttons to ensure they fit the new size
            createFloatingButtons();
        });

         // Simple custom message box function (replaces alert)
         function showMessage(message) {
            const messageBox = document.createElement('div');
            messageBox.classList.add(
                'fixed', 'top-1/2', 'left-1/2', 'transform', '-translate-x-1/2', '-translate-y-1/2',
                'bg-white', 'p-6', 'rounded-lg', 'shadow-xl', 'z-50', 'text-center',
                'max-w-sm', 'w-full'
            );
            messageBox.innerHTML = `
                <p class="text-lg font-semibold mb-4">${message}</p>
                <button class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600" onclick="this.parentElement.remove()">Close</button>
            `;
            document.body.appendChild(messageBox);
        }

        // Example of using the custom message box instead of alert
        // Replace alert(`You clicked the ${label} button!`); with:
        // showMessage(`You clicked the ${label} button!`);

    </script>

</body>
</html>
