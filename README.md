# Birdcall-Prediction--Denoising-Fragmentation
De-noising and fragmentation preprocessing algorithms for birdcall identification.<br/>
🐦 More info on the Kaggle competition and datasets can be found <a href="https://www.kaggle.com/c/birdsong-recognition">here.</a><br />

Here I develop a preprocessing algorithm of birdcalls sound data which automatically transforms records in order to remove regions of non-interest. These regions corresponds to the part of the audio files where the bird is not singing. <br />

In order to make the classification of the data easier using deep learning models, the algorithm first removes background noise. This helps the different datasets to become more distinct, as background noise is not relevant to the classification.  Finally, the algorithm ignores the regions of non-interest and converts the audio file into a shorter one containing just birdcalls. <br />

## De-noising algorithm
The de-noising algorithm has two parts:

* Noise-reduce function: let A be the space of bird audio files and D1 the space of partially de- noised audio files. This first step transforms the original audio files into partially de-noised audio files, maintaining their length.
nr: A ⟶ D1, audiooriginal ↦ nr(audiooriginal) = audio_(partially denoised).
* Band pass-filter: let D2 be the space of de-noised audio files. The second step transforms the
partially de-noised audio files into de-noised audio files, maintaining their length.
bf : D1 ⟶ D2 audiopartially denoised ↦ nr(audiopartially denoised) = audiodenoised
The de-noising algorithm can be understood as the composition of the above functions:
d = bf ∘ nr : A ⟶ D2, audiooriginal ↦ audiodenoised

Noise reduce is an already existing package to reduce noise for a general audio file. However, the inputs for the main function are a sample of background noise and the audio file itself.
Therefore, one cannot apply this method directly for this problem as we just have the audio file which we are interested on removing the background noise from.
Is it possible to adapt our audio files in order to take advantage of the noise-reduce package?
Idea: to create a new audio file from the existing audio file containing just background noise.
This was achieved by an optimisation algorithm that finds an optimum threshold depending on the type of audio separating those parts of the audio file that just contain noise from the ones that contain noise + bird call.

## Fragmentation algorithm
Fragmentation allows us to predict bird species and the times where they occur in the test data. By reducing size and removing non-bird call parts, fragmentation should also help with training speed/efficiency and/or performance. <br/>
This is my transformation approach to fragmentation; to fragment the audio files and to create new audio files coming from this fragmentation in order to reduce them in size but keeping the same valuable information.<br/>
Let F be the space of fragmented data. Our fragmentation can be understood as the function f : A ⟶ F, audiooriginal ↦ audiofragmented that transforms bird audio files into shorter audio files containing the characteristic bird calls for each audio files.<br/
The final algorithm applies the fragmentation to the de- noised data, i.e, we are considering:
f |D2 : D2 ⟶ F, audiodenoised ↦ audiofragmented
