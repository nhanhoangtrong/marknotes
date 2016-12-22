# Google Mobile Vision

>A library for face detection and tracking, can be using in iOS and Android

## `GMVDetector`

The main detector for detecting and tracking objects.

>A `GMVDetector` object uses image processing to search for and identify notable features in a still image or video.
>Detected features are represented by `GMVFeature` objects that provide more information about each feature.

* Need to Initialize before using, in needs a `GMVDetectorType` at first argument and a `NSDictionary` played as configuration options at second argument
* Some of `GMVDetectorType`:
    * `GMVDetectorTypeFace`: A detector that searches for faces in a still image or video, returning `GMVFaceFeature` objects that provide information about detected faces.
* Some of configuration options for the detector:
    * `GMVDetectorFaceMode`: A key used to specify detector's accuracy/speed trade-offs. If not specified, defaults to `GMVDetectorFaceFastMode`
    * `GMVDetectorFaceTrackingEnabled`: A key used to specify is face tracking feature enabled. If not specified, defaults to `false`.
    * `GMVDetectorFaceMinSize`: A key used to specify the smallest desired face size. The size is expressed as a proportion of the width of the head to the image width. For example, if a value of `0.1` is specified, then the smallest face to search for is roughly `10%` of the width of the image being searched. If not specified, defaults to `0.1`.
    * `GMVDetectorFaceClassificationType`: A key used to specify whether to run additional classifiers for characterizing attributes such as smiling. If not specified, defaults to `GMVDetectorFaceClassificationNone`.
    * `GMVDetectorFaceLandmarkType`: A key used to specify whether to detect no landmarks or all landmarks. Processing time increases as the number of landmarks to search for increases, so detecting all landmarks will increase the overall detection time. If not specified, defaults to `GMVDetectorFaceLandmarkNone`.
    * `GMVDetectorImageOrientation`: A key used to specify the display orientation of the image for face feature detection. The value of this key is an `NSNumber` wrapping a `GMVImageOrientation`.


## `GMVFeature` and `GMVFaceFeature`

## `GMVDataOutput`

### Initialize `AVCaptureSession` and add `GMVDataOutput`

* First we need to initialize a `AVCaptureSession`

```objective-c
_captureSession = [[AVCaptureSession alloc] init];
_captureSession.preset = AVCaptureSessionPresetMedium;
```

* Then we need to add an input source for `AVCaptureSession` via configurating camera position of `AVCaptureSession`:

```objc
[_captureSession beginConfiguration];
AVCaptureDevicePosition devicePostion = AVCaptureDevicePositionBack;

// Initialize input from devicePostion
AVCaptureDeviceInput *input = [self cameraInputFor:devicePostion];

// Then add input to captureSession
if (input) {
    [_captureSession addInput: input];
}

[_captureSession commitConfiguration];

```

* After adding input source, now we can initialize an `AVCaptureVideoPreviewLayer` for preview image and video capture

```objc
// Initialize preview layer
_previewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession: _captureSession];

// Some customization code
[self.previewLayer setBackgroundColor:[[UIColor whiteColor] CGColor]];
[self.previewLayer setVideoGravity:AVLayerVideoGravityResizeAspect];
CALayer *rootLayer = [self.placeholder layer];
[rootLayer setMasksToBounds:YES];
[self.previewLayer setFrame:[rootLayer bounds]];
[rootLayer addSublayer:self.previewLayer];
```

* To detect face or barcode we initialize a `GMVDetector` for later using in `GMVDataOutput`, there are also many types of `GMVDataOutput` such as: `GMVLargestFaceFocusingDataOutput` or `GMVMultiDataOutput` for using with this detector

```objc
NSDictionary *options = @{
    GMVDetectorFaceTrackingEnabled : @(YES),
    GMVDetectorFaceMode : @(GMVDetectorFaceFastMode),
    GMVDetectorFaceLandmarkType : @(GMVDetectorFaceLandmarkAll),
    GMVDetectorFaceClassificationType : @(GMVDetectorFaceClassificationAll),
    GMVDetectorFaceMinSize : @(!self.isBackCamera ? 0.35 : 0.15)
};
GMVDetector *detector = [GMVDetector detectorOfType:GMVDetectorTypeFace options:options];

// And intialize GMVDataOutput with this detector
_dataOutput = [[GMVMultiDataOutput alloc] initWithDetector:detector];
```

* For listening to feature output, we need to implement `GMVOutputTrackerDelegate`

```objc
((GMVLargestFaceFocusingDataOutput *)_dataOutput).trackerDelegate = tracker;
```

* For multiple feature output, we can implement `GMVMultiDataOutputDelegate`, and then with each feature received we can return a new `GMVOutputTrackerDelegate`

```objc
((GMVMultiDataOutput)_dataOutput).multiDataDelegate = multiDataDelegate;

#pragma mark - GMVMultiDataOutputDelegate

- (id<GMVOutputTrackerDelegate>)dataOutput:(GMVDataOutput *)dataOutput
                         trackerForFeature:(GMVFeature *)feature {
    FaceTracker *tracker = [[FaceTracker alloc] init];
    tracker.delegate = self;
    return tracker;
}
```

### `GMVOutputTrackerDelegate`

There are some important methods in this delegate:

```objc
- (void)dataOutput:(GMVDataOutput *)dataOutput detectedFeature:(GMVFeature *)feature;
```

Called when a new feature was detected.

>Called to initially inform the delegate that a new item has been detected. The tracking ID will remain constant for all calls to dataOutput:updateFocusingFeature:forResultSet: until dataOutputCompletedWithFocusingFeature: is called. dataOutput:updateFeatures:focusingFeature: is always called immediately following this method call, with the same item that was passed into this method.

```objc
- (void)dataOutput:(GMVDataOutput *)dataOutput updateFocusingFeature:(GMVFaceFeature *)face forResultSet:(NSArray<GMVFaceFeature *> *)features;
```

Called when feature is updating

>As a feature is detected over time, this method is called to give an update for the feature in the context of the overall detection. Each feature that is received has the same tracking ID as it had in the most recent call to dataOutput:detectedFeature:. That is, it has been determined to be the same tracked feature initially seen.

```objc
- (void)dataOutput:(GMVDataOutput *)dataOutput
updateMissingFeatures:(NSArray<GMVFaceFeature *> *)features;
```

Called when feature tracked feature was not detected in this frame

>Called if either the tracked feature was not detected at all in a frame, or if the specific feature identity associated with the delegate is not present in the current frame. It's possible that the feature may be missed for a few frames, and will reappear later, depending upon the detector settings. dataOutput:updateFocusingFeature:forResultSet: would then be called.

```objc
- (void)dataOutputCompletedWithFocusingFeature:(GMVDataOutput *)dataOutput;
```

Called when feature tracked is finished, no more available

>Called to indicate the feature associated with the tracking ID previously reported via dataOutput:detectedFeature: has been assumed to be gone forever.
