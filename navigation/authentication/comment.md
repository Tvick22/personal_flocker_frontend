---
layout: base
title: Comment
permalink: /comment
menu: nav/home.html
---

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Car Comments</title>
    <style>
        /* Styling */
        .comment-box {
            max-width: 500px;
            margin: 20px auto;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            background: #f9f9f9;
            font-family: Arial, sans-serif;
            text-align: center;
        }
        .fetch-button, .submit-button {
            display: block;
            margin: 10px auto;
            padding: 10px 20px;
            font-size: 16px;
            font-weight: bold;
            background: linear-gradient(45deg, #007bff, #00d4ff);
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: 0.3s ease-in-out;
        }
        .fetch-button:hover, .submit-button:hover {
            background: linear-gradient(45deg, #0056b3, #0094cc);
            transform: scale(1.05);
        }
        .input-field {
            width: 90%;
            padding: 10px;
            margin: 10px auto;
            font-size: 14px;
            border: 1px solid #ddd;
            border-radius: 8px;
            box-sizing: border-box;
        }
    </style>
</head>
<body>

<!-- Comment Display Box -->
<div class="comment-box" id="commentBox">
    <p><strong>Latest Comment:</strong></p>
    <p id="commentText">Click the button to fetch a comment.</p>
    <p id="commentUID"></p>
</div>

<!-- Comment Input -->
<div class="comment-box">
    <textarea id="newComment" class="input-field" placeholder="Write your comment here..."></textarea>
    <button id="submitCommentButton" class="submit-button">Submit Comment</button>
</div>

<!-- Fetch Button -->
<button id="fetchCommentButton" class="fetch-button">Fetch Comment</button>

<script type="module">
    import { getAllComments, postComment, deleteComment, updateComment } from "{{site.baseurl}}/assets/js/api/comments.js";

    const commentText = document.getElementById("commentText");
    const uidText = document.getElementById("commentUID");
    const newComment = document.getElementById("newComment");

    // Function to create a comment display with delete and edit buttons
    function createCommentDisplay(comment) {
        const commentBox = document.createElement("div");
        commentBox.className = "comment-box";

        const commentContent = document.createElement("p");
        commentContent.innerHTML = `<strong>Comment:</strong> ${comment.content}`;
        commentBox.appendChild(commentContent);

        const commentUID = document.createElement("p");
        commentUID.innerHTML = `Username: ${comment.user.name}`;
        commentBox.appendChild(commentUID);

        const deleteButton = document.createElement("button");
        deleteButton.className = "submit-button";
        deleteButton.innerHTML = "Delete Comment";
        deleteButton.addEventListener("click", async () => {
            const result = await deleteComment(comment.id);
            if (result.success) {
                alert("Comment deleted successfully!");
                commentBox.remove(); // Remove the comment box immediately
            } else {
                alert("Failed to delete comment.");
            }
        });
        commentBox.appendChild(deleteButton);

        const editButton = document.createElement("button");
        editButton.className = "submit-button";
        editButton.innerHTML = "Edit Comment";
        editButton.addEventListener("click", () => {
            const newContent = prompt("Edit your comment:", comment.content);
            if (newContent !== null) {
                updateComment(comment.id, newContent).then(result => {
                    if (result.success) {
                        alert("Comment updated successfully!");
                        commentContent.innerHTML = `<strong>Comment:</strong> ${newContent}`; // Update the comment content immediately
                    } else {
                        alert("Failed to update comment.");
                    }
                });
            }
        });
        commentBox.appendChild(editButton);

        return commentBox;
    }

    // Display all comments
    async function displayComment() {
        const comments = await getAllComments();
        const commentBox = document.getElementById("commentBox");
        commentBox.innerHTML = ""; // Clear existing content
        if (comments.length > 0) {
            comments.forEach(comment => {
                const newCommentDisplay = createCommentDisplay(comment);
                commentBox.appendChild(newCommentDisplay);
            });
        } else {
            commentBox.innerHTML = "<p>No comments available.</p>";
        }
    }

    // Submit a new comment
    async function submitComment() {
        const commentContent = newComment.value.trim();
        if (commentContent) {
            const result = await postComment({ content: commentContent, post_id: 1 });
            if (result.success) {
                alert("Comment submitted successfully!");
                newComment.value = "";
                displayComment(); // Optionally fetch and display the new comment
            } else {
                console.error('Failed to submit comment:', result);
                alert("Failed to submit comment.");
            }
        } else {
            alert("Comment cannot be empty.");
        }
    }

    document.addEventListener("DOMContentLoaded", () => {
        document.getElementById("fetchCommentButton").addEventListener("click", displayComment);
        document.getElementById("submitCommentButton").addEventListener("click", submitComment);
    });
</script>

</body>
