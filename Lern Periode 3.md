Der Trend der letzten Zeit: Ein Squid-Game-Spiel in Unity entwickeln


Vor Kurzem ist der zweite Squid-Game-Film erschienen, und plötzlich war die Serie wieder in aller Munde. Ich habe gemerkt, 
wie groß das Interesse daran immer noch ist, und dachte mir: Warum nicht versuchen, eines der bekanntesten Spiele, „Rotes Licht – Grünes Licht“, 
selbst in Unity zu programmieren? Es schien mir die perfekte Gelegenheit zu sein, etwas Trendiges umzusetzen und dabei meine Fähigkeiten weiterzuentwickeln.

```csharp
using System;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    [SerializeField] private Animator anim;
    [SerializeField] private LayerMask FloorMask;
    [SerializeField] private Transform FeetTransform, PlayerCamera, deathZone;
    [SerializeField] private Rigidbody PlayerBody;
    [SerializeField] private float Speed, Sensitivity, Jumpforce;
    [SerializeField] private AudioSource feetSteps, shoot;

    private Vector3 movementInput;
    private Vector2 mouseInput;
    private float xRot;
    private bool isJumping, isWalking, isDying, isInDeathZone;

    void Update()
    {
        if (isDying) return;

        movementInput = new Vector3(Input.GetAxis("Horizontal"), 0f, Input.GetAxis("Vertical"));
        mouseInput = new Vector2(Input.GetAxis("Mouse X"), Input.GetAxis("Mouse Y"));

        UpdateStates();
        MovePlayer();
        MoveCamera();
        HandleDeath();
    }

    private void UpdateStates()
    {
        isJumping = !Physics.CheckSphere(FeetTransform.position, 0.1f, FloorMask);
        isWalking = movementInput.magnitude > 0;

        anim.SetBool("isJumping", isJumping);
        anim.SetBool("isWalking", isWalking);

        if (isWalking && !isJumping && !feetSteps.isPlaying)
        {
            feetSteps.loop = true;
            feetSteps.Play();
        }
        else
        {
            feetSteps.loop = false;
        }
    }

    private void MovePlayer()
    {
        Vector3 moveVector = transform.TransformDirection(movementInput) * Speed;
        PlayerBody.velocity = new Vector3(moveVector.x, PlayerBody.velocity.y, moveVector.z);

        if (Input.GetKeyDown(KeyCode.Space) && !isJumping)
        {
            PlayerBody.AddForce(Vector3.up * Jumpforce, ForceMode.Impulse);
        }
    }

    private void MoveCamera()
    {
        xRot -= mouseInput.y * Sensitivity;
        transform.Rotate(0f, mouseInput.x * Sensitivity, 0f);
        PlayerCamera.localRotation = Quaternion.Euler(xRot, 0f, 0f);
    }

    private void HandleDeath()
    {
        if ((GameManager.headTime && (isWalking || isJumping)) || GameManager.headTimeFinish)
        {
            if (!isInDeathZone) return;

            isDying = true;
            anim.SetBool("isDying", true);
            feetSteps.Stop();
            shoot.Play();
        }
    }

    private void OnTriggerEnter(Collider other)
    {
        isInDeathZone = other.transform == deathZone;
    }

    private void OnTriggerExit(Collider other)
    {
        if (other.transform == deathZone) isInDeathZone = false;
    }
}
```

<img width="1690" alt="Screenshot 2025-01-10 at 11 27 29" src="https://github.com/user-attachments/assets/a80db62f-9465-4048-b1e0-f8172fb8b8b0" />















17.01.2025
Heute habe ich an einem VR-Spiel mit Apple Xcode gearbeitet, anders als Unity.
Seit es VR-Brillen gibt, beschäftige ich mich intensiv mit diesem Thema. Mit Xcode ein VR-Spiel zu entwickeln, war eine Herausforderung. Ich habe oft Hilfe aus dem Internet geholt, weil es anders als Unity ist und Sprachen wie Swift, Python und C++ verwendet. Trotzdem habe ich es geschafft, ein 3D-Jenga-Spiel zu erstellen, das mit einer VR-Brille gespielt wird.

Früher habe ich ein Spiel im Play Store veröffentlicht. Aber ich denke, dass die echten Erfolgschancen bei Anwendungen für diese neuen VR-Brillen liegen. Im Play Store gibt es über 3,5 Millionen und im App Store fast 2 Millionen Apps. Es gibt dort also viele Konkurrenten, und es ist sehr schwer, aufzufallen.

Mit Xcode und der Apple Vision Pro Plattform gibt es dagegen aktuell nur etwa 1000 Apps. Viele große Marken wie Instagram, Facebook oder Snapchat haben noch keine Anwendungen dort. Deshalb sehe ich hier eine große Chance. Solange der Markt neu ist und es nur wenige Konkurrenten gibt, werde ich weitere VR-Brillen-Apps entwickeln, bis eine davon erfolgreich wird.
<img width="1728" alt="Screenshot 2025-01-17 at 15 44 27" src="https://github.com/user-attachments/assets/e597fcf7-a96c-4b4b-9be5-8ffd2da6dded" />
<img width="949" alt="Screenshot 2025-01-17 at 15 44 30" src="https://github.com/user-attachments/assets/57c9d27b-01a6-4872-98ec-fb1a2f886524" />




<img width="1728" alt="Screenshot 2025-01-17 at 11 23 01" src="https://github.com/user-attachments/assets/8cdac015-799c-400f-8fd1-88328c42105b" />
<img width="1721" alt="Screenshot 2025-01-17 at 11 22 53" src="https://github.com/user-attachments/assets/c79971ae-d4af-4751-a813-0d9ae25760c4" />
<img width="1728" alt="Screenshot 2025-01-17 at 11 39 01" src="https://github.com/user-attachments/assets/dee0a736-ad5c-4b01-88b0-99f5f735d670" />



```swift


//
//  ImmersiveView.swift
//  Jenga
//
//  Created by Donovan Hutchinson on 26/06/2024.
//

import SwiftUI

import SwiftUI
import RealityKit
import RealityKitContent

struct ImmersiveView: View {
    @ObservedObject var viewModel: SharedViewModel

    var body: some View {
        RealityView { content in
            let floor = viewModel.generateFloor()
            content.add(floor)

            if let table = loadScene() {
                table.position.x = viewModel.startingPositionX + viewModel.pieceWidth
                table.position.y = viewModel.startingPositionY
                table.position.z = viewModel.startingPositionZ - viewModel.pieceWidth
           
                content.add(table)
            }
            
            // Add pieces
            content.add(viewModel.tower)
        } update: { content in

        }
        .installGestures()
    }
    
    private func loadScene() -> Entity? {
      try? Entity.load(named: "Scene", in: realityKitContentBundle)
    }
}

#Preview {
    ImmersiveView(viewModel: SharedViewModel())
}
```


