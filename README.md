
# 👻 Slender Man VR

## 📌 Introduction
**Slender Man VR** is an immersive horror experience where players must navigate through eerie environments while being stalked by the enigmatic Slender Man. The game features a procedurally generated map, dynamic AI behavior, and a chilling audiovisual experience to create an unsettling atmosphere.

🔗 Video Trailer

https://youtu.be/_1BsBJsM6us

![image](https://github.com/user-attachments/assets/dc5cf700-be19-4c10-9071-e05ae9dc87f5)

## 🎮 Features
- 🎭 **Slender Man AI**: Dynamically adjusts its behavior based on player proximity and collected objectives.
- 🌲 **Procedural Map Generation**: Ensures unique experiences in every playthrough.
- 📜 **Collectible Pages**: Players must gather scattered notes while evading Slender Man.
- 🚷 **Survival Mechanics**: Avoid direct eye contact and manage flashlight battery levels.
- 🎵 **Immersive Audio Design**: Includes whispers, ambient noises, and Slender Man's static distortion.
- 🎨 **VHS & Glitch Effects**: Enhances the psychological horror elements.
- 🕹️ **VR Hand & Player Movement**: Supports smooth locomotion and hand interactions.

---

## 🏗️ How It Works
The game comprises various interactive elements, AI-driven behavior, and a fear-inducing atmosphere. Players use VR controls to move, interact, and survive against Slender Man.

---

### 👣 **Player Movement (Continuous Locomotion)**
Handles movement and speed adjustments based on player actions.

```csharp
using UnityEngine;
using UnityEngine.InputSystem;
using UnityEngine.XR.Interaction.Toolkit;

[RequireComponent(typeof(ContinuousMoveProviderBase))]
public class PlayerMovement : MonoBehaviour
{
    [SerializeField] InputActionReference sprintAction;
    [SerializeField] ContinuousMoveProviderBase moveSystem;
    [SerializeField] float sprintSpeed;
    [SerializeField] float normalSpeed;

    private void Awake()
    {
        if (!sprintAction) Debug.LogError("Sprint action not assigned!");
        if (!moveSystem) moveSystem = GetComponent<ContinuousMoveProviderBase>();
    }

    void Update()
    {
        moveSystem.moveSpeed = sprintAction.action.ReadValue<float>() > 0 ? sprintSpeed : normalSpeed;
    }
}
```

---

### 🕵️ **Slender Man AI Behavior**
Slender Man follows the player, teleporting unpredictably while avoiding direct visibility.

```csharp
using UnityEngine;

public class SlenderAI : MonoBehaviour
{
    [SerializeField] Transform player;
    [SerializeField] float minTeleportDistance;
    [SerializeField] float maxTeleportDistance;
    [SerializeField] float detectionRadius;
    [SerializeField] AudioClip staticSound;
    [SerializeField] AudioSource audioSource;

    private void Update()
    {
        float distance = Vector3.Distance(transform.position, player.position);
        if (distance < detectionRadius)
        {
            TeleportAway();
            audioSource.PlayOneShot(staticSound);
        }
    }

    private void TeleportAway()
    {
        Vector3 randomOffset = Random.insideUnitSphere * Random.Range(minTeleportDistance, maxTeleportDistance);
        randomOffset.y = 0;
        transform.position = player.position + randomOffset;
    }
}
```

---

### 📜 **Collectible Page System**
Tracks player progress and triggers Slender Man’s aggression as more pages are collected.

```csharp
using UnityEngine;
using System;

public class PageCollector : MonoBehaviour
{
    public static event Action<int> PageCollected;
    private int pagesCollected = 0;

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Page"))
        {
            pagesCollected++;
            PageCollected?.Invoke(pagesCollected);
            Destroy(other.gameObject);
        }
    }
}
```

---

### 🎵 **VHS Distortion Effect**
Applies static interference when Slender Man is near.

```csharp
using UnityEngine;

public class VHSFilter : MonoBehaviour
{
    [SerializeField] Material vhsMaterial;
    [SerializeField] AudioSource staticNoise;
    private bool isDistorted = false;

    void Update()
    {
        if (Vector3.Distance(transform.position, SlenderMan.Instance.transform.position) < 10f && !isDistorted)
        {
            isDistorted = true;
            vhsMaterial.SetFloat("_GlitchIntensity", 1.0f);
            staticNoise.Play();
        }
        else if (Vector3.Distance(transform.position, SlenderMan.Instance.transform.position) > 10f && isDistorted)
        {
            isDistorted = false;
            vhsMaterial.SetFloat("_GlitchIntensity", 0.0f);
            staticNoise.Stop();
        }
    }
}
```

---

### 🔦 **Flashlight System**
Simulates a battery-powered flashlight with limited usage.

```csharp
using UnityEngine;

public class Flashlight : MonoBehaviour
{
    [SerializeField] Light flashlight;
    [SerializeField] float batteryLife = 100f;
    private bool isOn = true;

    void Update()
    {
        if (Input.GetButtonDown("ToggleFlashlight"))
        {
            isOn = !isOn;
            flashlight.enabled = isOn;
        }
        if (isOn)
        {
            batteryLife -= Time.deltaTime;
            if (batteryLife <= 0) flashlight.enabled = false;
        }
    }
}
```

---

## 🎯 Conclusion
**Slender Man VR** delivers an adrenaline-pumping horror experience with unpredictable AI, procedural level generation, and immersive audiovisual effects. Players must collect all pages while evading Slender Man’s relentless pursuit. Dare to survive the night? 👀🎮
