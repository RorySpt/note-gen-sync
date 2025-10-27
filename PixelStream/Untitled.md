像素流送-使用远程信令服务器按钮
PixelStreamingToolbar.cpp:45

```cpp
PluginCommands->MapAction(
			FPixelStreamingCommands::Get().ExternalSignalling,
			FExecuteAction::CreateLambda([]() {
				IPixelStreamingEditorModule::Get().UseExternalSignallingServer(!IPixelStreamingEditorModule::Get().UseExternalSignallingServer());
				IPixelStreamingEditorModule::Get().StopSignalling();
			}),
```

发现使用`FPixelStreamingEditorModule`

FPixelStreamingEditorModule::StartupModule()
--- FPixelStreamingEditorModule::InitEditorStreaming(IPixelStreamingModule& Module
-------- EditorStreamer = Module.CreateStreamer(EditorStreamerID);

```
IMainFrameModule::Get().OnMainFrameCreationFinished().AddLambda([&](TSharedPtr<SWindow> RootWindow, bool bIsRunningStartupDialog) {
		MaybeResizeEditor(RootWindow);

		if (UE::EditorPixelStreaming::Settings::CVarEditorPixelStreamingStartOnLaunch.GetValueOnAnyThread())
		{
			···
			StartStreaming(Source);
		}
	});
```

FPixelStreamingEditorModule在模块启动时创建EditorStreamer ，然后对它做各种配置，等待`IMainFrameModule::Get().OnMainFrameCreationFinished()` 执行`StartStreaming(Source)`

`StartStreaming(Source)``继续做各种配置：

```
switch (InStreamType)
	{
		case UE::EditorPixelStreaming::EStreamTypes::LevelEditorViewport:
		{
			···
			EditorStreamer->SetTargetViewport(SceneViewport->GetViewportWidget());
			EditorStreamer->SetTargetWindow(SceneViewport->FindWindow());
			EditorStreamer->SetInputHandlerType(EPixelStreamingInputType::RouteToWindow);
			EditorStreamer->SetVideoInput(FPixelStreamingVideoInputViewport::Create(EditorStreamer));
		}
		break;
		case UE::EditorPixelStreaming::EStreamTypes::Editor:
		{
			EditorStreamer->SetTargetViewport(nullptr);
			EditorStreamer->SetTargetWindow(nullptr);
			EditorStreamer->SetInputHandlerType(EPixelStreamingInputType::RouteToWindow);

			TSharedPtr<FPixelStreamingVideoInputBackBufferComposited> VideoInput = FPixelStreamingVideoInputBackBufferComposited::Create();
			VideoInput->OnFrameSizeChanged.AddSP(EditorStreamer.ToSharedRef(), &IPixelStreamingStreamer::SetTargetScreenRect);
			EditorStreamer->SetVideoInput(VideoInput);
		}
		break;
	}
EditorStreamer->StartStreaming();
```

EditorStreamer 的类型为 `class FStreamer : public IPixelStreamingStreamer, public TSharedFromThis<FStreamer>`

`EditorStreamer->SetVideoInput(FPixelStreamingVideoInputViewport::Create(EditorStreamer));`
--- `VideoSourceGroup->SetVideoInput(Input);`
------ `VideoInput->OnFrameCaptured.AddSP(AsShared(), &FVideoSourceGroup::OnFrameCaptured);`

`FVideoSourceGroup::OnFrameCaptured);`
--- `FVideoSourceGroup::Tick();`

```
void FVideoSourceGroup::Tick()
	{
		···
		for (auto& VideoSource : VideoSources)
		{
			if (VideoSource)
			{
				VideoSource->MaybePushFrame();
			}
		}
	}
```

