import React, { useState, useEffect } from "react";

export default function JuegoPezGato() {
  const [pos, setPos] = useState({ x: 50, y: 50 });
  const [points, setPoints] = useState([]);
  const [score, setScore] = useState(0);
  const [home, setHome] = useState({ x: 90, y: 90 });

  useEffect(() => {
    const newPoints = Array.from({ length: 5 }, () => ({
      x: Math.random() * 90,
      y: Math.random() * 90
    }));
    setPoints(newPoints);
  }, []);

  useEffect(() => {
    const handleKey = (e) => {
      setPos((prev) => {
        let { x, y } = prev;
        if (e.key === "ArrowUp") y -= 2;
        if (e.key === "ArrowDown") y += 2;
        if (e.key === "ArrowLeft") x -= 2;
        if (e.key === "ArrowRight") x += 2;
        return {
          x: Math.max(0, Math.min(100, x)),
          y: Math.max(0, Math.min(100, y))
        };
      });
    };
    window.addEventListener("keydown", handleKey);
    return () => window.removeEventListener("keydown", handleKey);
  }, []);

  useEffect(() => {
    setPoints((prev) =>
      prev.filter((p) => {
        const hit =
          Math.abs(p.x - pos.x) < 3 && Math.abs(p.y - pos.y) < 3;
        if (hit) setScore((s) => s + 1);
        return !hit;
      })
    );
  }, [pos]);

  const reachedHome =
    score >= 5 &&
    Math.abs(pos.x - home.x) < 5 &&
    Math.abs(pos.y - home.y) < 5;

  return (
    <div className="w-full h-screen bg-blue-200 relative overflow-hidden">
      <h1 className="text-xl p-2">Puntaje: {score}</h1>

      {/* Pez gato */}
      <div
        className="absolute text-3xl"
        style={{ left: `${pos.x}%`, top: `${pos.y}%` }}
      >
        🐟
      </div>

      {/* Puntos */}
      {points.map((p, i) => (
        <div
          key={i}
          className="absolute text-yellow-500"
          style={{ left: `${p.x}%`, top: `${p.y}%` }}
        >
          ⭐
        </div>
      ))}

      {/* Casa */}
      <div
        className="absolute text-3xl"
        style={{ left: `${home.x}%`, top: `${home.y}%` }}
      >
        🏠
      </div>

      {reachedHome && (
        <div className="absolute top-1/2 left-1/2 text-2xl bg-white p-4 rounded-2xl">
          ¡Ganaste! 🎉
        </div>
      )}

      <p className="absolute bottom-2 left-2 text-sm">
        Usa las flechas ⬆️⬇️⬅️➡️ para mover el pez
      </p>
    </div>
  );
}
