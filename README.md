
# speechtotext


const sdk = require("microsoft-cognitiveservices-speech-sdk");
const fs = require("fs");
const fmpeg = require("fluent-ffmpeg")
const subscriptionKey = "febc326d8a104c948f5e455f9e2898ca";
const serviceRegion = "westus"; // e.g., "westus"
const filename = "test4.wav"; // 16000 Hz, Mono
const language = "en-US";
var file = fmpeg(filename)

// file.audioCodec("pcm_s16le")
// .audioBitrate("256k")
// .audioChannels(1)
// .audioFrequency(16000)
// .format("wav")
// .save("final.wav")


function createAudioConfig(filename) {
    const pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filename).on('data', arrayBuffer => {
        
        pushStream.write(arrayBuffer.slice());
    }).on('end', () => {
        pushStream.close();
    });

    return sdk.AudioConfig.fromStreamInput(pushStream);
}

function createRecognizer(audiofilename, audioLanguage) {
    const audioConfig = createAudioConfig(audiofilename);
    const speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
    speechConfig.speechRecognitionLanguage = audioLanguage;

    return new sdk.SpeechRecognizer(speechConfig, audioConfig);
}

let recognizer = createRecognizer(filename, language);



recognizer.recognizeOnceAsync(
    result => {
        console.log(result.text);
        console.log("output")
        recognizer.close();
        recognizer = undefined;
    },
    err => {
        console.trace("err - " + err);

        recognizer.close();
        recognizer = undefined;
    });
