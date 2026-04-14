## Hi there 👋

import { useState } from "react";

export default function App() {
  const [username, setUsername] = useState("");
  const [user, setUser] = useState(null);
  const [repos, setRepos] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  const fetchUser = async () => {
    if (!username) return;
    setLoading(true);
    setError("");
    setUser(null);
    setRepos([]);

    try {
      const userRes = await fetch(`https://api.github.com/users/${username}`);
      if (!userRes.ok) throw new Error("User not found");
      const userData = await userRes.json();

      const repoRes = await fetch(userData.repos_url);
      const repoData = await repoRes.json();

      setUser(userData);
      setRepos(repoData);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gray-100 flex flex-col items-center p-6">
      <h1 className="text-3xl font-bold mb-4">GitHub User Search</h1>

      <div className="flex gap-2 mb-6">
        <input
          type="text"
          placeholder="Enter GitHub username"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
          className="p-2 border rounded-lg"
        />
        <button
          onClick={fetchUser}
          className="bg-blue-500 text-white px-4 py-2 rounded-lg"
        >
          Search
        </button>
      </div>

      {loading && <p>Loading...</p>}
      {error && <p className="text-red-500">{error}</p>}

      {user && (
        <div className="bg-white shadow-md rounded-xl p-4 w-full max-w-md">
          <img
            src={user.avatar_url}
            alt="avatar"
            className="w-24 h-24 rounded-full mx-auto"
          />
          <h2 className="text-xl font-semibold text-center mt-2">
            {user.name || user.login}
          </h2>
          <p className="text-center text-gray-600">
            Public Repos: {user.public_repos}
          </p>
        </div>
      )}

      {repos.length > 0 && (
        <div className="mt-6 w-full max-w-md">
          <h3 className="text-xl font-bold mb-2">Repositories</h3>
          <ul className="space-y-2">
            {repos.map((repo) => (
              <li
                key={repo.id}
                className="bg-white p-3 rounded-lg shadow"
              >
                <a
                  href={repo.html_url}
                  target="_blank"
                  rel="noreferrer"
                  className="text-blue-600 font-semibold"
                >
                  {repo.name}
                </a>
              </li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}

