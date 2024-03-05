# Learning

Key calling sequence: https://github.com/hi-ogawa/blog/blob/master/src/posts/2017-03-03-blink-overview--vol-3-.md

Hit Testing intro : http://smnh.me/hit-testing-in-ios/

A good explanation of WebGL V.S. WebGPU https://gigazine.net/gsc_news/en/20200427-experimental-webgpu/

## Concepts

Page, Frame, Document, ExecutionContext and DOMWindow are the following concepts:

A Page corresponds to a concept of a tab (if OOPIF explained below is not enabled). Each renderer process may contain multiple tabs.
A Frame corresponds to a concept of a frame (the main frame or an iframe). Each Page may contain one or more Frames that are arranged in a tree hierarchy.
A DOMWindow corresponds to a window object in JavaScript. Each Frame has one DOMWindow.
A Document corresponds to a window.document object in JavaScript. Each Frame has one Document.
An ExecutionContext is a concept that abstracts a Document (for the main thread) and a WorkerGlobalScope (for a worker thread).

Renderer process : Page = 1 : N.

Page : Frame = 1 : M.

Frame : DOMWindow : Document (or ExecutionContext) = 1 : 1 : 1 at any point in time, but the mapping may change over time. For example, consider the following code:

iframe.contentWindow.location.href = "https://example.com";

In this case, a new DOMWindow and a new Document are created for https://example.com. However, the Frame may be reused.

(Note: Precisely speaking, there are some cases where a new Document is created but the DOMWindow and the Frame are reused. There are even more complex cases.)

Out-of-Process iframes (OOPIF)
Site Isolation makes things more secure but even more complex. :) The idea of Site Isolation is to create one renderer process per site. (A site is a page’s registrable domain + 1 label, and its URL scheme. For example, https://mail.example.com and https://chat.example.com are in the same site, but https://noodles.com and https://pumpkins.com are not.) If a Page contains one cross-site iframe, the Page may be hosted by two renderer processes. Consider the following page:

<!-- https://example.com -->
<body>
<iframe src="https://example2.com"></iframe>
</body>

The main frame and the <iframe> may be hosted by different renderer processes. A frame local to the renderer process is represented by LocalFrame and a frame not local to the renderer process is represented by RemoteFrame.

From the perspective of the main frame, the main frame is a LocalFrame and the <iframe> is a RemoteFrame. From the perspective of the <iframe>, the main frame is a RemoteFrame and the <iframe> is a LocalFrame.

Communications between a LocalFrame and RemoteFrame (which may exist in different renderer processes) are handled via the browser process.

If you want to learn more:

Design docs: Site isolation design docs
How to write code with site isolation: core/frame/SiteIsolation.md
Detached Frame / Document
Frame / Document may be in a detached state. Consider the following case:

doc = iframe.contentDocument;
iframe.remove();  // The iframe is detached from the DOM tree.
doc.createElement("div");  // But you still can run scripts on the detached frame.

The tricky fact is that you can still run scripts or DOM operations on the detached frame. Since the frame has already been detached, most DOM operations will fail and throw errors. Unfortunately, behaviors on detached frames are not really interoperable among browsers nor well-defined in the specs. Basically the expectation is that JavaScript should keep running but most DOM operations should fail with some proper exceptions, like this:

void someDOMOperation(...) {
  if (!script_state_->ContextIsValid()) { // The frame is already detached
    …;  // Set an exception etc
    return;
  }
}

This means that in common cases Blink needs to do a bunch of clean-up operations when the frame gets detached. You can do this by inheriting from ContextLifecycleObserver, like this:

class SomeObject : public GarbageCollected<SomeObject>, public ContextLifecycleObserver {
  void ContextDestroyed() override {
    // Do clean-up operations here.
  }
  ~SomeObject() {
    // It's not a good idea to do clean-up operations here because it's too late to do them. Also a destructor is not allowed to touch any other objects on Oilpan's heap.
  }
};


// RenderView corresponds to the content container of a renderer's subset
// of the frame tree. A frame tree that spans multiple renderers will have a
// RenderView in each renderer, containing the local frames that belong to
// that renderer. The RenderView holds non-frame-related state that is
// replicated across all renderers, and is a fairly shallow object.
// Generally, most APIs care about state related to the page content which
// should be accessed through RenderFrame instead.
//
// WARNING: Historically RenderView was the path to get to the main frame,
// and the entire frame tree, but that is no longer the case. Usually
// RenderFrame is a more appropriate surface for new code, unless the code is
// agnostic of frames and page content or structure. For more context, please
// see https://crbug.com/467770 and
// https://www.chromium.org/developers/design-documents/site-isolation.

// RenderFrame interface wraps functionality, which is specific to frames, such as
// navigation. It provides communication with a corresponding RenderFrameHost
// in the browser process.


// WebContents is the core class in content/. A WebContents renders web content
// (usually HTML) in a rectangular area.
//
// Instantiating one is simple:
//   std::unique_ptr<content::WebContents> web_contents(
//       content::WebContents::Create(
//           content::WebContents::CreateParams(browser_context)));
//   gfx::NativeView view = web_contents->GetNativeView();
//   // |view| is an HWND, NSView*, etc.; insert it into the view hierarchy
//   // wherever it needs to go.
//
// That's it; go to your kitchen, grab a scone, and chill. WebContents will do
// all the multi-process stuff behind the scenes. More details are at
// https://www.chromium.org/developers/design-documents/multi-process-architecture
// .
//
// Each WebContents has exactly one NavigationController; each
// NavigationController belongs to one WebContents. The NavigationController can
// be obtained from GetController(), and is used to load URLs into the
// WebContents, navigate it backwards/forwards, etc. See navigation_controller.h
// for more details.



## Scenarios
. Layout : https://chromium.googlesource.com/chromium/src/+/master/third_party/blink/renderer/core/layout/README.md
. Element Geometry:  https://docs.google.com/document/d/1WZKlOSUK4XI0Le0fgCsyUTVw0dTwutZXGWwzlHXewiU/preview#
. Scrolling: https://docs.google.com/presentation/d/1pwx0qBW4wSmYAOJxq2gb3SMvSTCHz2L2TFx_bjsvm8E/preview?slide=id.p
	
	
	
## WebGPU
https://surma.dev/things/webgpu/
	


