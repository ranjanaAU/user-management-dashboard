const API_URL = "https://jsonplaceholder.typicode.com/users";
let users = [];

document.addEventListener("DOMContentLoaded", () => {
   
    fetchUsers();

    
    document.getElementById('addUserButton').addEventListener('click', () => {
        showForm();
    });
});


async function fetchUsers() {
    try {
        const response = await fetch(API_URL);
        users = await response.json();
        renderUserList();
    } catch (error) {
        alert('Error fetching users!');
    }
}


function renderUserList() {
    const userTableBody = document.getElementById("userTableBody");
    userTableBody.innerHTML = ''; // Clear previous list

    users.forEach(user => {
        const userRow = document.createElement("tr");
        userRow.innerHTML = `
            <td>${user.id}</td>
            <td>${user.name.split(' ')[0]}</td>
            <td>${user.name.split(' ')[1]}</td>
            <td>${user.email}</td>
            <td>${user.company.name}</td>
            <td>
                <button onclick="editUser(${user.id})">Edit</button>
                <button onclick="deleteUser(${user.id})">Delete</button>
            </td>
        `;
        userTableBody.appendChild(userRow);
    });
}


function showForm(user = null) {
    document.getElementById('userForm').classList.remove('hidden');

    
    document.getElementById('formTitle').textContent = user ? 'Edit User' : 'Add User';
    document.getElementById('firstName').value = user ? user.name.split(' ')[0] : '';
    document.getElementById('lastName').value = user ? user.name.split(' ')[1] : '';
    document.getElementById('email').value = user ? user.email : '';
    document.getElementById('department').value = user ? user.company.name : '';

    
    const submitButton = document.getElementById('submitButton');
    if (user) {
        submitButton.onclick = (event) => submitEditForm(event, user.id);
    } else {
        submitButton.onclick = submitAddForm;
    }
}


async function submitAddForm(event) {
    event.preventDefault();

    
    const email = document.getElementById('email').value;
    if (!isValidEmail(email)) {
        alert('Please enter a valid email address.');
        return; // Stop further execution if email is invalid
    }

    const newUser = {
        name: `${document.getElementById('firstName').value} ${document.getElementById('lastName').value}`,
        email: email,
        company: { name: document.getElementById('department').value },
    };

    try {
        const response = await fetch(API_URL, {
            method: 'POST',
            body: JSON.stringify(newUser),
            headers: { 'Content-Type': 'application/json' },
        });
        const addedUser = await response.json();
        users.push(addedUser);
        renderUserList();
        cancelForm();
    } catch (error) {
        alert('Error adding user!');
    }
}


async function submitEditForm(event, userId) {
    event.preventDefault();

    
    const email = document.getElementById('email').value;
    if (!isValidEmail(email)) {
        alert('Please enter a valid email address.');
        return; // Stop further execution if email is invalid
    }

    const updatedUser = {
        name: `${document.getElementById('firstName').value} ${document.getElementById('lastName').value}`,
        email: email,
        company: { name: document.getElementById('department').value },
    };

    try {
        const response = await fetch(`${API_URL}/${userId}`, {
            method: 'PUT',
            body: JSON.stringify(updatedUser),
            headers: { 'Content-Type': 'application/json' },
        });
        const updatedUserData = await response.json();

        const index = users.findIndex(user => user.id === userId);
        users[index] = updatedUserData;

        renderUserList();
        cancelForm();
    } catch (error) {
        alert('Error updating user!');
    }
}


function isValidEmail(email) {
    const emailPattern = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
    return emailPattern.test(email);
}


async function deleteUser(userId) {
    try {
        await fetch(`${API_URL}/${userId}`, { method: 'DELETE' });
        users = users.filter(user => user.id !== userId);
        renderUserList();
    } catch (error) {
        alert('Error deleting user!');
    }
}


function cancelForm() {
    document.getElementById('userForm').classList.add('hidden');
    clearFormFields();
}


function clearFormFields() {
    document.getElementById('firstName').value = '';
    document.getElementById('lastName').value = '';
    document.getElementById('email').value = '';
    document.getElementById('department').value = '';
}


function editUser(userId) {
    const user = users.find(user => user.id === userId);
    if (user) {
        showForm(user);
    }
}





