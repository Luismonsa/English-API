# English-API
Repositorio con fines didácticos
// Speak It Right - Pronunciation Tutor (React + PWA Compatible)

import { useState, useRef } from "react";

const sounds = [
  { symbol: "/ɪ/", word: "bit", audio: "/audio/bit.mp3" },
  { symbol: "/iː/", word: "beat", audio: "/audio/beat.mp3" },
  { symbol: "/æ/", word: "cat", audio: "/audio/cat.mp3" },
  { symbol: "/ʌ/", word: "cup", audio: "/audio/cup.mp3" },
  { symbol: "/θ/", word: "think", audio: "/audio/think.mp3" },
];

export default function PronunciationApp() {
  const [selectedSound, setSelectedSound] = useState(null);
  const [recording, setRecording] = useState(false);
  const [audioURL, setAudioURL] = useState(null);
  const mediaRecorderRef = useRef(null);
  const chunksRef = useRef([]);

  const playAudio = (url) => {
    const audio = new Audio(url);
    audio.play();
  };

  const startRecording = async () => {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
    mediaRecorderRef.current = new MediaRecorder(stream);
    mediaRecorderRef.current.ondataavailable = (e) => chunksRef.current.push(e.data);
    mediaRecorderRef.current.onstop = () => {
      const blob = new Blob(chunksRef.current, { type: 'audio/mp3' });
      setAudioURL(URL.createObjectURL(blob));
      chunksRef.current = [];
    };
    mediaRecorderRef.current.start();
    setRecording(true);
  };

  const stopRecording = () => {
    mediaRecorderRef.current.stop();
    setRecording(false);
  };

  return (
    <div className="min-h-screen bg-white p-6 text-center">
      <h1 className="text-2xl font-bold mb-4">🎧 Speak It Right</h1>
      <p className="text-gray-600 mb-6">Haz clic en un símbolo para escuchar su sonido en inglés británico.</p>
      <div className="grid grid-cols-2 gap-4 max-w-md mx-auto">
        {sounds.map((sound, index) => (
          <button
            key={index}
            className="bg-blue-100 hover:bg-blue-200 rounded-xl p-4 shadow"
            onClick={() => {
              setSelectedSound(sound);
              playAudio(sound.audio);
            }}
          >
            <div className="text-xl font-semibold">{sound.symbol}</div>
            <div className="text-sm text-gray-700">({sound.word})</div>
          </button>
        ))}
      </div>

      {selectedSound && (
        <div className="mt-8">
          <h2 className="text-lg font-medium">Practicando: {selectedSound.word}</h2>
          <p className="text-gray-500 mb-2">Repite el sonido después de escucharlo.</p>
          <div className="flex flex-col items-center gap-4">
            <button
              className={`px-4 py-2 rounded text-white ${recording ? 'bg-red-500' : 'bg-green-500'}`}
              onClick={recording ? stopRecording : startRecording}
            >
              {recording ? "🛑 Detener grabación" : "🎙️ Grabar mi voz"}
            </button>
            {audioURL && (
              <audio controls src={audioURL} className="mt-2" />
            )}
          </div>
        </div>
      )}
    </div>
  );
}

