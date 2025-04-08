# Project Summary: Jaimla (Conceptual State: v0.3.0-alpha Planning)

## Introduction

Jaimla is a project focused on creating an interactive, virtual AI agent designed to run within a standard web browser. Its core differentiator lies in using web technologies (HTML/CSS) for the agent's visual representation, rendered within a 3D space managed by Three.js via the `CSS3DRenderer`.

## Core Concepts

* **CSS3D Agent:** The agent's visual appearance (head, body, eyes, heart, etc.) is built dynamically using DOM elements, allowing familiar web techniques for styling and animation (GSAP).
* **AI-Driven Styling:** A key concept where the agent's visual style (colors, borders, effects) can be dynamically changed based on context (emotion, user requests, conversation tone), driven by AI interpretation of descriptive prompts or direct commands (potentially from a backend API).
* **Modular Architecture:** The agent's functions are separated into distinct JavaScript `Controller` classes (e.g., `VisualController`, `BehaviorController`, `PerceptionController`, `MemoryController`, `AIController`, `GoalManager`) for better organization and extensibility.

## Architecture & Current State (v0.3.0-alpha Planning)

The project design has evolved towards a **client-server architecture**. The client-side Jaimla agent (JavaScript code described herein) primarily acts as an **interface and orchestrator**:

**Input Gathering (`PerceptionController`):** Captures user input (text chat, clicks, drops, feedback) and provides hooks for integrating real-time audio/speech/visual data via browser APIs (implementation pending).
**Backend Communication (`AIController`):** Packages gathered inputs and client state, sending requests to a dedicated backend **multimodal API** (assumed to be available externally). Handles receiving and parsing potentially complex responses.
**Instruction Execution (`BehaviorController`):** Interprets instructions received from the backend API (e.g., text to display/speak, gestures to perform, emotions to express, styles to apply, goals to set) and coordinates the relevant client-side controllers (`Visual`, `Motion`, `SpeechSynthesis`, `GoalManager`, etc.) to execute them.
**State & Persistence (`MemoryController`, `GoalManager`):** Manages client-side state, conversation history, and user preferences, with basic persistence implemented via `localStorage`. The `GoalManager` structure handles potentially complex, asynchronous, and hierarchical tasks (often initiated by the backend API).
**Visual/Auditory Output (`VisualController`, Helpers):** Renders the agent's appearance, displays chat, visualizes emotion ('Luvai Heart'), performs animations, and uses `SpeechSynthesis` (placeholder integration) for spoken output.

Placeholders remain for core AI model integration (now assumed largely backend), browser API implementations (audio/speech/D3), and advanced features like robust behavioral adaptation. Conceptual logic for handling API errors and potential offline fallback modes has been discussed.

## Key Development Steps Covered (Conceptual Design Phase)

* Established base v0.0.9 structure.
* Refined AI styling placeholders (v0.1.1, v0.1.3).
* Added Chat UI structure (v0.1.1).
* Implemented Memory Persistence (v0.1.2).
* Added UI Feedback hooks (v0.1.2).
* Improved Logging/Error Handling (v0.1.2).
* Implemented Preference Learning hooks (v0.1.3).
* Outlined Behavioral Adaptation concepts/hooks (v0.1.3, v0.1.5).
* Refined GoalManager for Async/Hierarchy/Errors/Interruptions (v0.1.4, v0.1.5).
* Shifted architecture towards client-API model based on multimodal backend (v0.3.0-alpha planning).
* Discussed Fallback mechanisms and API data structures (v0.3.0-alpha planning).

## Next Steps

The immediate next steps involve the **actual implementation** of the designed structure:

Integrate the client-side code with the specific **backend multimodal API** endpoints and data formats.
Implement the **browser API integrations** outlined in `PerceptionController` (Web Audio, SpeechRecognition, potentially WebRTC/Canvas for visual input).
Implement **SpeechSynthesis** for spoken output.
Replace remaining client-side AI **placeholders** (`aiStyleGenerator`, `adaptBehavior` logic) with functional code (either simple client-side logic or logic driven by the backend API's responses).
Implement **D3.js** charting if required.
**Performance testing and optimization**, especially concerning CSS3D rendering and frequent updates.

## Long-Term Vision

Future goals include developing advanced cognitive capabilities (potentially driven by the backend), robust learning and adaptation, deeper multimodal understanding, performance optimization for wider deployment (including potential local/handheld scenarios), and potentially building a framework or community around this architecture.


Audit and improve Jailma as expression

```txt
jaimla.prompt focus on improving Jaimla "as expression." This means refining how Jaimla conveys its internal state, personality, and reactions through its visual appearance, animations, and communication style, based on the v0.2.0-alpha conceptual structure.
```
Audit of Current Expressive Capabilities (v0.2.0-alpha concept):

Visual Style: Changes based on prompts/API responses. Transitions rely on basic CSS transitions or direct style application. Weakness: Can be abrupt; placeholder logic limits nuance.
Facial Expression: Basic discrete emotions map to eye/mouth changes via GSAP. Eye tracking adds dynamism. Weakness: Limited range, lacks intensity scaling, potentially robotic.
Gestures: Limited library (e.g., 'wave'). Weakness: Needs expansion and better context linking.
Emotional Heart: 'Luvai Heart' changes color/scale/speed based on emotion tag + intensity. Strength: Dedicated emotional channel. Weakness: Needs integration with other expressions for consistency.
Text Output: Content dictated by API/placeholders. Displayed uniformly. Weakness: Lacks stylistic variation based on mood/context.
Speech Output: Basic text-to-speech via browser API. Weakness: Monotone by default, lacks emotional inflection, no lip sync.
Proposed Improvements for Expression:

# Enhanced Consistency and Orchestration (BehaviorController)

The BehaviorController should act as the central orchestrator for expression, ensuring all channels are aligned with the current internal state (emotion, task focus, etc.), whether set internally or by the backend API.

```js

class BehaviorController {
    // ... existing properties ...
    currentEmotion = 'neutral';
    currentEmotionIntensity = 0.5;

    // Refined method to update *all* expressive channels based on state
    updateExpressiveState(newState = {}) {
        const updatedEmotion = newState.emotion !== undefined ? newState.emotion : this.currentEmotion;
        const updatedIntensity = newState.emotionIntensity !== undefined ? newState.emotionIntensity : this.currentEmotionIntensity;
        const taskFocus = newState.taskFocus || agentObject.goalManager?.getActiveGoal()?.goal.name || 'idle'; // e.g., 'thinking', 'responding', 'listening'

        console.log(`[Log] Behavior: Updating Expressive State - Emotion: ${updatedEmotion} (${updatedIntensity.toFixed(2)}), Task: ${taskFocus}`);

        // --- Update Internal State ---
        this.currentEmotion = updatedEmotion;
        this.currentEmotionIntensity = updatedIntensity;
        // Update other relevant state based on newState...

        // --- Orchestrate Expressive Controllers ---
        // Update Heart (already takes intensity)
        updateHeart(this.currentEmotion, this.currentEmotionIntensity);

        // Update Facial Expression (pass intensity)
        agentObject.visual?.playFacialExpression(this.currentEmotion, this.currentEmotionIntensity);

        // Update Body Language / Gestures (conceptual)
        agentObject.motion?.setPosture(this.currentEmotion, this.currentEmotionIntensity); // e.g., slump when sad, alert when thinking
        // Trigger context-appropriate gestures occasionally?

        // Update Style (Generate prompt reflecting current state)
        const styleContext = {
            emotion: this.currentEmotion,
            intensity: this.currentEmotionIntensity,
            task: taskFocus,
            // Add other relevant context from memory/perception
        };
        const stylePrompt = agentObject.stylePrompter?.generateStylePrompt(styleContext);
        if (stylePrompt) {
             // Consider animating the style transition if VisualController supports it
             agentObject.visual?.animateToStyle(stylePrompt, styleContext); // New method needed
        }

        // Speech parameters will be adjusted when speakAgentResponse is called
    }

    // Call updateExpressiveState when emotion changes or API provides newState
    updateEmotion(newEmotion, intensity = 0.5) {
        this.updateExpressiveState({ emotion: newEmotion, emotionIntensity: intensity });
        // Remember the state change
        agentObject.memory?.remember({ type: 'emotion_state', emotion: newEmotion, intensity: intensity, timestamp: Date.now() });
    }

    async processApiResponse(response) {
        // ... (existing processing) ...
        // Update state based on API response AFTER executing actions
        if (response.newState) {
            this.updateExpressiveState(response.newState); // Centralized update
        }
        // ...
    }
    // ... rest of BehaviorController ...
}
```
# Nuanced Visuals and Animations
(VisualController, MotionController)<br />

Intensity Scaling: Modify playFacialExpression and gesture methods to accept an intensity parameter (0.0 to 1.0) and use it to scale the animation effects (e.g., wider smile at higher 'happy' intensity, faster/sharper movements for high-intensity 'angry').
Smoother Animation (GSAP): Utilize more sophisticated GSAP easings (elastic, bounce, slowmo) and timeline overlaps to make movements appear more organic and less robotic. Animate style transitions smoothly.
Gesture Library: Define more gestures in MotionController linked to communicative intents (e.g., 'thinking' pose, 'explaining' hand movement, 'confused' head tilt, 'nod'). BehaviorController would trigger these based on context or API instructions.
Idle Variations: Add subtle procedural animations during the 'idle' state (managed by BehaviorController calling AnimationController or MotionController) like eye blinks (quick opacity/scale change on eye elements), slight random head drifts, or background layer shimmering.
Animated Style Transitions: Create a new VisualController.animateToStyle(prompt, context) method. Instead of instantly applying styles from aiStyleGenerator, use GSAP to tween CSS properties (colors, borders, transforms, opacity) over a duration (e.g., 0.5s - 1.5s) for smoother visual shifts.

```js
// --- Conceptual additions ---
class VisualController {
    // ...
    playFacialExpression(expressionType, intensity = 0.5) {
        console.log(`[Log] Visual: Playing expression ${expressionType} (Intensity: ${intensity.toFixed(2)})`);
        // Base values
        const baseEyeScale = { surprise: 1.1, happy: 1.0, neutral: 1.0, sad: 0.9, angry: 0.95 };
        // ... other base values ...
        // Apply intensity scaling
        const targetEyeScale = 1.0 + ((baseEyeScale[expressionType] || 1.0) - 1.0) * intensity; // Scale effect based on intensity
        // ... calculate other target values based on intensity ...
        gsap.to(/* ... eyes ... */ { scale: targetEyeScale, /* ... */ });
        gsap.to(/* ... mouth ... */ { /* ... intensity-scaled properties ... */ });
        // ... intensity-scaled head tilt ...
    }

    animateToStyle(stylePrompt, context = {}) {
        console.log(`[Log] Visual: Animating to style for prompt "${stylePrompt}"`);
        const targetStyles = this.aiStyleGenerator(stylePrompt, context);
        const duration = 0.75; // Animation duration

        for (const elementKey in targetStyles) {
            if (this.elements[elementKey]) {
                gsap.to(this.elements[elementKey].style, {
                    ...targetStyles[elementKey], // Target CSS properties
                    duration: duration,
                    ease: 'power1.inOut'
                    // Note: GSAP can tween many CSS properties directly
                });
            }
        }
    }
    // ...
}

class MotionController {
    // ...
    setPosture(emotion, intensity) { /* Use GSAP to subtly adjust body/head rotation/position */ }
    performGesture(gestureName, intensity = 0.5) {
        console.log(`[Log] Motion: Performing gesture ${gestureName} (Intensity: ${intensity.toFixed(2)})`);
        // Implement library of gestures (thinking, shrug, point, nod, etc.)
        // Use intensity to affect speed, range of motion, or sharpness
    }
    // ...
}
```
# Expressive Communication (Speech & Text)

Vocal Intonation: Modify speakAgentResponse to adjust pitch and rate based on the agent's current emotion and intensity. Higher intensity might mean faster rate, higher pitch for excitement/anger, lower pitch for sadness.
Lip Sync Placeholder: Integrate placeholder calls agentObject.visual.startLipSync() and agentObject.visual.stopLipSync() within speakAgentResponse (triggered by utterance.onstart / utterance.onend). The VisualController methods would contain simple mouth animations (e.g., rapidly changing agentMouth height/shape) - accurate lip sync is very complex.
Text Style Variation: While CSS3D makes direct text styling complex, the content of the text generated (by the backend API or AIController) could vary. BehaviorController could pass hints (like responseVerbosity or desired formality) to the AI call.
JavaScript
```js
// --- Refined speakAgentResponse ---
function speakAgentResponse(textToSpeak) {
    // ... (API support check) ...
    const utterance = new SpeechSynthesisUtterance(textToSpeak);
    utterance.lang = 'en-US';

    // --- Adjust parameters based on agent state ---
    const emotion = agentObject.behavior?.currentEmotion || 'neutral';
    const intensity = agentObject.behavior?.currentEmotionIntensity || 0.5;

    let rate = 1.0;
    let pitch = 1.0;

    if (emotion === 'happy' || emotion === 'surprised') { rate = 1.1 + (intensity * 0.2); pitch = 1.1 + (intensity * 0.2); }
    else if (emotion === 'angry') { rate = 1.1 + (intensity * 0.3); pitch = 0.9 - (intensity * 0.2); } // Faster, slightly lower?
    else if (emotion === 'sad') { rate = 0.9 - (intensity * 0.2); pitch = 0.8 - (intensity * 0.2); } // Slower, lower

    utterance.rate = Math.max(0.5, Math.min(2.0, rate)); // Clamp values
    utterance.pitch = Math.max(0.5, Math.min(2.0, pitch));

    console.log(`[Log] Speech: Speaking with Rate=${utterance.rate.toFixed(2)}, Pitch=${utterance.pitch.toFixed(2)} for Emotion=${emotion}`);

    utterance.onstart = () => { console.log("Speech: Started."); agentObject.visual?.startLipSync(); };
    utterance.onend = () => { console.log("Speech: Finished."); agentObject.visual?.stopLipSync(); };
    utterance.onerror = (event) => { console.error("Speech: Error", event.error); agentObject.visual?.stopLipSync(); };

    speechSynthesis.speak(utterance);
}
```

This complete Jaimla as a conceptual module ready for futher improvement
