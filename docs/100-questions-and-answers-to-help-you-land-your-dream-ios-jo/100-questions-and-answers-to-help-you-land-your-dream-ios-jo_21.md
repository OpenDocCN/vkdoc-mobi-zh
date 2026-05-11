# 问题 87：为什么我们应该在 viewDidUnload 中释放 outlets？

`viewDidUnload` 严格用于释放具有 retain 属性的 `IBOutlet`。原因与 `UIViewController` 有一个它持有的 view 属性有关。该 view 属性本身持有对其所有子视图的引用。这些子视图正是你在这些 outlet 属性中持有的。问题在于这些子视图有一个“额外”的 retain。

`-viewDidUnload` 的目标是清理不必要的内存使用。当调用 `-viewDidUnload` 时，view 属性已经被释放，从而释放了顶层的 `UIView` 及其所有子视图。然而，由于我们持有了其中一些子视图，它们会滞留在内存中，而我们想要释放它们，因为它们将不再被使用。当（如果）视图重新加载时，将创建这些子视图的新副本。严格来说，属性也会被设置为 `nil`，这样我们就不会让指针指向已释放的内存。

