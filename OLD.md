'use client';

import { useState } from 'react';

export default function Home() {
  const [setupDone, setSetupDone] = useState(false);
  const [numPlayers, setNumPlayers] = useState(2);
  const [playerNames, setPlayerNames] = useState([]);
  const [playerBalances, setPlayerBalances] = useState([]);
  const [players, setPlayers] = useState([]);
  const [pot, setPot] = useState(0);
  const [betAmounts, setBetAmounts] = useState({});

  // Wenn Anzahl Spieler geändert wird, Arrays anpassen
  const handleNumPlayersChange = (n) => {
    setNumPlayers(n);
    setPlayerNames(Array(n).fill(''));
    setPlayerBalances(Array(n).fill('0'));
  };

  function startGame() {
    const initialPlayers = Array.from({ length: numPlayers }, (_, i) => ({
      id: i + 1,
      name: playerNames[i] || `Spieler ${i + 1}`,
      balance: parseInt(playerBalances[i]) || 0,
    }));
    setPlayers(initialPlayers);
    setSetupDone(true);
  }

  function bet(playerId) {
    const amount = parseInt(betAmounts[playerId]) || 0;
    if (amount <= 0) return;

    if (players.find(p => p.id === playerId).balance < amount) {
      alert('Nicht genug Guthaben!');
      return;
    }

    setPlayers(players.map(p =>
      p.id === playerId && p.balance >= amount
        ? { ...p, balance: p.balance - amount }
        : p
    ));
    setPot(pot + amount);
    setBetAmounts({ ...betAmounts, [playerId]: '' });
  }

  function assignWinner(playerId) {
    setPlayers(players.map(p =>
      p.id === playerId ? { ...p, balance: p.balance + pot } : p
    ));
    setPot(0);
  }

  if (!setupDone) {
    return (
      <div className="min-h-screen flex items-center justify-center bg-gray-900 text-white p-6">
        <div className="bg-gray-800 p-6 rounded-xl shadow-lg w-full max-w-lg">
          <h1 className="text-3xl font-bold mb-4 text-center">Poker Setup</h1>

          <label className="block mb-4">
            Anzahl Spieler:
            <input
              type="number"
              value={numPlayers}
              onChange={e => handleNumPlayersChange(parseInt(e.target.value))}
              min="1"
              className="ml-2 px-2 py-1 rounded bg-gray-700 text-white"
            />
          </label>

          {Array.from({ length: numPlayers }, (_, i) => (
            <div key={i} className="mb-4">
              <h2 className="font-semibold mb-1">Spieler {i + 1}</h2>
              <input
                type="text"
                placeholder="Name"
                value={playerNames[i] || ''}
                onChange={e => {
                  const newNames = [...playerNames];
                  newNames[i] = e.target.value;
                  setPlayerNames(newNames);
                }}
                className="w-full mb-1 px-2 py-1 rounded bg-gray-700 text-white"
              />
              <input
                type="number"
                placeholder="Startbetrag"
                value={playerBalances[i] || ''}
                onChange={e => {
                  const newBalances = [...playerBalances];
                  newBalances[i] = e.target.value;
                  setPlayerBalances(newBalances);
                }}
                className="w-full px-2 py-1 rounded bg-gray-700 text-white"
              />
            </div>
          ))}

          <button
            onClick={startGame}
            className="w-full bg-blue-600 hover:bg-blue-500 py-2 rounded-lg font-bold"
          >
            Spiel starten
          </button>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-900 text-white p-6">
      <h1 className="text-4xl font-bold text-center mb-6">Poker Tracker</h1>
      <h2 className="text-xl mb-4 text-center">Pot: {pot}</h2>
      <ul className="space-y-4 max-w-md mx-auto">
        {players.map(p => (
          <li
            key={p.id}
            className="bg-gray-800 p-4 rounded-xl shadow-lg flex flex-col items-center"
          >
            <div className="text-lg font-semibold mb-2">{p.name}: {p.balance}</div>
            <div className="flex gap-2">
              <input
                type="number"
                value={betAmounts[p.id] || ''}
                onChange={e => setBetAmounts({ ...betAmounts, [p.id]: e.target.value })}
                placeholder="Einsatz"
                className="px-2 py-1 rounded bg-gray-700 text-white w-24"
              />
              <button
                onClick={() => bet(p.id)}
                className="bg-green-600 hover:bg-green-500 px-3 py-1 rounded-lg"
              >
                Setzen
              </button>
              <button
                onClick={() => assignWinner(p.id)}
                className="bg-yellow-600 hover:bg-yellow-500 px-3 py-1 rounded-lg"
              >
                Pot zuordnen
              </button>
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
}
