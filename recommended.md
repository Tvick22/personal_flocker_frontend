---
layout: base
search_exclude: true
menu: nav/home.html
---
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Favorites and Listings</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            line-height: 1.6;
        }
        .container {
            margin-bottom: 20px;
        }
        .favorite-item, .listing {
            border: 1px solid #ddd;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
        }
        .listing-image {
            max-width: 100%;
            height: auto;
        }
        button {
            background-color: #007BFF;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 3px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        .update-prompt {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
}
.update-overlay {
position: absolute;
top: 0;
left: 0;
width: 100%;
height: 100%;
background-color: rgba(0, 0, 0, 0.5);
}
.update-box {
position: relative;
background: white;
padding: 20px;
border-radius: 10px;
box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
z-index: 1001;
}
.update-actions {
margin-top: 10px;
display: flex;
justify-content: space-between;
}
.update-actions button {
padding: 5px 10px;
border: none;
border-radius: 5px;
cursor: pointer;
}

</style>
</head>
<body>
    <div class="container">
        <h1>Favorites</h1>
        <button id="display-favorites-button">Display Favorites</button>
        <div id="favorites-container"></div>
    </div>

<div class="container">
    <h1>Listings</h1>
    <div id="listings-container"></div>
</div>

<script type="module">
    import { getListings } from '{{site.baseurl}}/assets/js/api/listings.js';
    import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

    document.addEventListener("DOMContentLoaded", () => {
        const favoritesContainer = document.getElementById("favorites-container");
        const displayFavoritesButton = document.getElementById("display-favorites-button");

        // Function to fetch and display favorites
        async function get_favorites() {
            try {
                const response = await fetch(`${pythonURI}/api/itemStore`, {
                    method: 'GET',
                    mode: 'cors',
                    cache: 'default',
                    credentials: 'include',
                    headers: {
                        'Content-Type': 'application/json',
                        'X-Origin': 'client'
                    }
                });

                if (!response.ok) {
                    throw new Error('Failed to fetch favorites');
                }

                const data = await response.json();
                console.log(data);

                // Clear old favorites
                favoritesContainer.innerHTML = '';

                // Populate favorites container with data
                data.forEach(item => {
                    const favoriteElement = document.createElement('div');
                    favoriteElement.classList.add('favorite-item');
                    favoriteElement.innerHTML = `
                        <h3>${item.name}</h3>
                        <h3>${item.user_input}</h3> 
                        <button class="update-button" data-id="${item.id}" data-name="${item.name}">Update Comment</button>
                        <button class="delete-button" data-name="${item.name}">Delete</button>
                    `;
                    favoritesContainer.appendChild(favoriteElement);
                });

                // Add event listeners to delete buttons
                const deleteButtons = document.querySelectorAll(".delete-button");
                deleteButtons.forEach(button => {
                    button.addEventListener("click", async (event) => {
                        const itemName = event.target.getAttribute("data-name");
                        await delete_favorite(itemName);
                    });
                });
                
                favoritesContainer.addEventListener('click', (event) => {
                if (event.target.classList.contains('update-button')) {
                    const itemName = event.target.getAttribute('data-name');
                    const itemId = event.target.getAttribute('data-id');


                    // Create the update comment prompt
                    const updatePrompt = document.createElement('div');
                    updatePrompt.classList.add('update-prompt');
                    updatePrompt.innerHTML = `
                        <div class="update-overlay"></div>
                        <div class="update-box">
                            <h3>Update Comment for ${itemName}</h3>
                            <input type="text" id="new-comment" placeholder="Enter new comment" />
                            <div class="update-actions">
                                <button id="save-comment">Save</button>
                                <button id="close-prompt">Close</button>
                            </div>
                        </div>
                    `;
                    document.body.appendChild(updatePrompt);

                    // Close the prompt
                    document.getElementById('close-prompt').addEventListener('click', () => {
                        document.body.removeChild(updatePrompt);
                    });

                    // Save the new comment
                    document.getElementById('save-comment').addEventListener('click', () => {
                        const newComment = document.getElementById('new-comment').value;
                        if (newComment.trim() !== '') {
                            update_comment(newComment, itemId)
                            console.log(`Saving new comment for ${itemName}: ${newComment}`);
                            // Perform update logic here (e.g., updating the UI or sending data to the server)

                            // Example: Update the comment in the UI
                            const favoriteItems = document.querySelectorAll('.favorite-item');
                            favoriteItems.forEach(item => {
                                if (item.querySelector('h3').textContent === itemName) {
                                    const commentElement = item.querySelector('h3:nth-of-type(2)');
                                    commentElement.textContent = newComment;
                                }
                            });

                            // Remove the prompt after saving
                            document.body.removeChild(updatePrompt);
                        } else {
                            alert('Please enter a valid comment.');
                        }
                    });
                }
            });






            } catch (error) {
                console.error(error);
                favoritesContainer.innerHTML = '<p>Error loading favorites. Please try again.</p>';
            }
        }

        // Function to delete a favorite
        async function delete_favorite(itemName) {
            try {
                const response = await fetch(`${pythonURI}/api/itemStore`, {
                    method: 'DELETE',
                    mode: 'cors',
                    cache: 'default',
                    credentials: 'include',
                    headers: {
                        'Content-Type': 'application/json',
                        'X-Origin': 'client'
                    },
                    body: JSON.stringify({ name: itemName })
                });

                if (!response.ok) {
                    throw new Error('Failed to delete favorite');
                }

                const data = await response.json();
                alert(data.message);
                await get_favorites(); // Refresh the favorites list
            } catch (error) {
                console.error('Error deleting favorite:', error);
                alert('An unexpected error occurred while deleting the item.');
            }
        }

        async function update_comment(newComment, id) {
            try {
                const response = await fetch(`${pythonURI}/api/itemStore`, {
                    method: 'PUT',
                    mode: 'cors',
                    cache: 'default',
                    credentials: 'include',
                    headers: {
                        'Content-Type': 'application/json',
                        'X-Origin': 'client'
                    },
                    body: JSON.stringify({ id: id, user_input: newComment })
                });

                if (!response.ok) {
                    throw new Error('Failed to delete favorite');
                }

                const data = await response.json();
                alert(data.message);
                await get_favorites(); // Refresh the favorites list
            } catch (error) {
                console.error('Error deleting favorite:', error);
                alert('An unexpected error occurred while deleting the item.');
            }
        }

        // Attach an event listener to the "Display Favorites" button
        if (displayFavoritesButton) {
            displayFavoritesButton.addEventListener('click', async () => {
                await get_favorites();
            });
        } else {
            console.error('Display Favorites button not found');
        }

        // Fetch and display listings
        getListings().then((listings) => {
            const listingsContainer = document.getElementById("listings-container");

            if (!listingsContainer) {
                console.error('Listings container not found');
                return;
            }

            listings.forEach(listing => {
                const listingElement = document.createElement("div");
                listingElement.classList.add("listing");

                const content = `
                    <img src="${listing.picture}" alt="${listing.name}" class="listing-image" />
                    <h2>${listing.name}</h2>
                    <p><strong>Type:</strong> ${listing.type}</p>
                    <p><strong>Mileage:</strong> ${listing.mileage}</p>
                    <p><strong>Price:</strong> ${listing.price}</p>
                    <button class="favorite-button" data-name="${listing.name}">Add to Favorites</button>
                    <input type="text" id="${listing.name}-comment" placeholder="Enter a comment">
                `;
                listingElement.innerHTML = content;
                listingsContainer.appendChild(listingElement);
            });

            const favoriteButtons = document.querySelectorAll(".favorite-button");

            favoriteButtons.forEach(button => {
                button.addEventListener("click", async (event) => {
                    const listingName = event.target.getAttribute("data-name");
                    const listingComment = document.getElementById(listingName+"-comment").value;

                    try {
                        const response = await fetch(`${pythonURI}/api/itemStore`, {
                            method: 'POST',
                            mode: 'cors',
                            cache: 'default',
                            credentials: 'include',
                            headers: {
                                'Content-Type': 'application/json',
                                'X-Origin': 'client'
                            },
                            body: JSON.stringify({ name: listingName, user_input: listingComment })
                        });

                        if (response.ok) {
                            const data = await response.json();
                            alert(`Listing '${data.name}' has been added to your favorites!`);
                        } else {
                            const error = await response.json();
                            alert(`Error: ${error.message}`);
                        }
                    } catch (error) {
                        console.error('Failed to add favorite:', error);
                        alert('An unexpected error occurred.');
                    }
                });
            });
        });
    });
</script>
</body>
