import React, { useState, useEffect } from 'react';
import axios from 'axios';

const apiBase = 'http://localhost:5000';

function App() {
  const [games, setGames] = useState([]);
  const [form, setForm] = useState({ title: '', genre: '', description: '', releaseDate: '', rating: '' });
  const [editId, setEditId] = useState(null);

  useEffect(() => {
    fetchGames();
  }, []);

  const fetchGames = async () => {
    const res = await axios.get(`${apiBase}/games`);
    setGames(res.data);
  };

  const handleChange = (e) => {
    setForm({ ...form, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (editId) {
      await axios.put(`${apiBase}/games/${editId}`, form);
    } else {
      await axios.post(`${apiBase}/games`, form);
    }
    setForm({ title: '', genre: '', description: '', releaseDate: '', rating: '' });
    setEditId(null);
    fetchGames();
  };

  const handleEdit = (game) => {
    setForm({
      title: game.title,
      genre: game.genre,
      description: game.description,
      releaseDate: game.releaseDate.substring(0,10),
      rating: game.rating,
    });
    setEditId(game._id);
  };

  const handleDelete = async (id) => {
    await axios.delete(`${apiBase}/games/${id}`);
    fetchGames();
  };

  return (
    <div style={{ padding: '20px' }}>
      <h1>Game Collection</h1>
      <form onSubmit={handleSubmit} style={{ marginBottom: '20px' }}>
        <input
          name="title"
          placeholder="Title"
          value={form.title}
          onChange={handleChange}
          required
        />
        <input
          name="genre"
          placeholder="Genre"
          value={form.genre}
          onChange={handleChange}
          required
        />
        <input
          name="description"
          placeholder="Description"
          value={form.description}
          onChange={handleChange}
        />
        <input
          type="date"
          name="releaseDate"
          value={form.releaseDate}
          onChange={handleChange}
        />
        <input
          type="number"
          name="rating"
          placeholder="Rating"
          value={form.rating}
          onChange={handleChange}
        />
        <button type="submit">{editId ? 'Update' : 'Add'} Game</button>
        {editId && (
          <button
            type="button"
            onClick={() => {
              setForm({ title: '', genre: '', description: '', releaseDate: '', rating: '' });
              setEditId(null);
            }}
          >
            Cancel
          </button>
        )}
      </form>
      <ul>
        {games.map((game) => (
          <li key={game._id} style={{ marginBottom: '10px' }}>
            <h3>{game.title}</h3>
            <p>Genre: {game.genre}</p>
            <p>Description: {game.description}</p>
            <p>Release Date: {new Date(game.releaseDate).toLocaleDateString()}</p>
            <p>Rating: {game.rating}</p>
            <button onClick={() => handleEdit(game)}>Edit</button>
            <button onClick={() => handleDelete(game._id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
