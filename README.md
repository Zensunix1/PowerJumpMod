using System;
using System.Collections.Generic;
using HarmonyLib;
using BepInEx;
using UnityEngine;
using System.Reflection;
using UnityEngine.XR;
using Photon.Pun;
using System.IO;
using System.Net;
using Photon.Realtime;
using UnityEngine.Rendering;

namespace RunnerMonke
{
    [BepInPlugin("org.ZixedTheMonkeyGuy.monkeytag.RunnerMonke", "RunnerMonke", "1.00")]
    public class MyPatcher : BaseUnityPlugin
    {
        public void Awake()
        {
            var harmony = new Harmony("com.Zixed.monkeytag.RunnerMonke");
            harmony.PatchAll(Assembly.GetExecutingAssembly());
        }
    }

    [HarmonyPatch(typeof(GorillaLocomotion.Player))]
    [HarmonyPatch("Update", MethodType.Normal)]

    public class Class1
    {
        static bool Run = false;
        static void PostFix(GorillaLocomotion.Player __instance)
        {
            if(!PhotonNetwork.CurrentRoom.IsVisible || !PhotonNetwork.InRoom)
            {
                List<InputDevice> list = new List<InputDevice>();
                InputDevices.GetDevicesWithCharacteristics(UnityEngine.XR.InputDeviceCharacteristics.HeldInHand | UnityEngine.XR.InputDeviceCharacteristics.Right | UnityEngine.XR.InputDeviceCharacteristics.Controller, list);
                list[0].TryGetFeatureValue(CommonUsages.gripButton, out Run);

                if(Run)
                {
                    __instance.jumpMultiplier = 10f;
                    __instance.maxJumpSpeed = 10f;
                }
                else
                {
                    __instance.jumpMultiplier = 1f;
                    __instance.maxJumpSpeed = 1f;
                }
            }
        }
    }
}

