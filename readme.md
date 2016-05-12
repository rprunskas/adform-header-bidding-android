# Integrating AdInline with a RecyclerView

The Adform Advertising SDK includes a RecyclerView.Adapter class called `AdformRecyclerViewAdapter`. This class automatically inserts AdInline banners into your content.

## Basic integration

Create `AdformRecyclerViewAdapter ` adapter using original adapter reference, master tag, ad size, and `AdformRecyclerViewAdParams` interface implementation. You have to return a number in `showAdEveryNTime()` method which specifies how often ad will be shown in the `RecyclerView`, this number should be greater than 0. If you want to use custom layout params, you have to implement `createAdRelativeLayoutParams `, return null to stick with default params.

```java
	RecyclerView recyclerView = (RecyclerView) yourView.findViewById(R.id.your_recycler_view);
    recyclerView.setLayoutManager(yourLayoutManager);

	AdformRecyclerViewAdapter adformRecyclerViewAdapter 
            = new AdformRecyclerViewAdapter(adapter, your-master-tag, new AdSize(320,50), new AdformRecyclerViewAdParams() {

        @Override
        public int showAdEveryNTime() {
            return 3;
        }
	
        @Override
        @Nullable
        public RelativeLayout.LayoutParams createAdRelativeLayoutParams() {
            RelativeLayout.LayoutParams layoutParams 
                = new RelativeLayout.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
            layoutParams.addRule(RelativeLayout.CENTER_HORIZONTAL, RelativeLayout.TRUE);
            layoutParams.addRule(RelativeLayout.CENTER_VERTICAL, RelativeLayout.TRUE);
            return layoutParams;
        }
    });
    
	recyclerView.setAdapter(adformRecyclerViewAdapter);

```