# Module

 // retrofit, gson
    compile 'com.google.code.gson:gson:2.6.2'
    compile 'com.squareup.retrofit2:retrofit:2.0.2'
    compile 'com.squareup.retrofit2:converter-gson:2.0.2'


//ApiClient
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;


public class ApiClient {

   // public static final String BASE_URL = "http://api.themoviedb.org/3/";
    public static final String BASE_URL = "http://datarow.in/1KickRide/";
    private static Retrofit retrofit = null;


    public static Retrofit getClient() {
        if (retrofit==null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}


//ApiInterface


import okhttp3.MultipartBody;
import okhttp3.RequestBody;
import retrofit2.Call;
import retrofit2.http.Field;
import retrofit2.http.FormUrlEncoded;
import retrofit2.http.GET;
import retrofit2.http.Multipart;
import retrofit2.http.POST;
import retrofit2.http.Part;


public interface ApiInterface {

    @FormUrlEncoded
    @POST("index.php/Home/TestSearch")
    Call<CommonEntity> ResponseTestSearch(@Field("testSearch") String testSearch);


}



//CommonEntity

import com.google.gson.annotations.SerializedName;

import java.util.ArrayList;

/**
 * Created by CHANDAN on 10/17/2018.
 */

public class CommonEntity {

    @SerializedName("name")
    public ArrayList<ArrayCommonEntity> arrayCommonEntities;

    public ArrayList<ArrayCommonEntity> getArrayCommonEntities() {
        return arrayCommonEntities;
    }

    public void setArrayCommonEntities(ArrayList<ArrayCommonEntity> arrayCommonEntities) {
        this.arrayCommonEntities = arrayCommonEntities;
    }
}

For Direct Json Array

@SerializedName("name")
    @Expose
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    
    in Fragment
    List<CommonEntity> list = null;
    
    list = response.body();
    

//import com.google.gson.annotations.SerializedName;

/**
 * Created by CHANDAN on 10/17/2018.
 */

public class ArrayCommonEntity {
    @SerializedName("name")
    public String Name;

    public String getName() {
        return Name;
    }

    public void setName(String name) {
        Name = name;
    }
}


//Fragment Search



import android.content.Intent;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.text.Editable;
import android.text.TextWatcher;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.EditText;
import android.widget.LinearLayout;

import com.example.rajeev.testfragmentsotp.R;
import com.example.rajeev.testfragmentsotp.rest.AdapterSearch;
import com.example.rajeev.testfragmentsotp.rest.ApiClient;
import com.example.rajeev.testfragmentsotp.rest.ApiInterface;
import com.example.rajeev.testfragmentsotp.rest.ArrayCommonEntity;
import com.example.rajeev.testfragmentsotp.rest.CommonEntity;

import java.util.ArrayList;

import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;

/**
 * A simple {@link Fragment} subclass.
 */
public class FragmentSearch extends Fragment implements TextWatcher {


    private View view;
    /**
     * Search Location
     */
    private EditText mChooseLocationSearch;
    private RecyclerView mRecyclerView;

    ArrayList<ArrayCommonEntity> commonEntityArrayList;

    public FragmentSearch() {
        // Required empty public constructor
    }


    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.fragment_search, container, false);
        commonEntityArrayList = new ArrayList<>();
        initView(rootView);
        return rootView;
    }

    private void initView(View rootView) {
        mChooseLocationSearch = (EditText) rootView.findViewById(R.id.chooseLocationSearch);
        mRecyclerView = (RecyclerView) rootView.findViewById(R.id.recyclerView);
        mChooseLocationSearch.addTextChangedListener(this);
    }

    @Override
    public void beforeTextChanged(CharSequence s, int start, int count, int after) {

    }

    @Override
    public void onTextChanged(CharSequence s, int start, int before, int count) {
        commonEntityArrayList.clear();
        if(s.toString().length()>0){
            callServerMethod(s.toString());
        }else {
            mRecyclerView.setAdapter(null);
        }
    }



    @Override
    public void afterTextChanged(Editable s) {



    }

    private void callServerMethod(String testSearch) {

        ApiInterface apiService = ApiClient.getClient().create(ApiInterface.class);

        apiService.ResponseTestSearch(testSearch).enqueue(new Callback<CommonEntity>() {
            @Override
            public void onResponse(Call<CommonEntity> call, Response<CommonEntity> response) {
                int statusCode = response.code();


                if (response.isSuccessful())
                    if (response.body() != null) {
                        commonEntityArrayList = response.body().getArrayCommonEntities();
                        for (int i = 0; i <commonEntityArrayList.size() ; i++) {
                            System.out.println(commonEntityArrayList.get(i).getName());
                        }

                        AdapterSearch adapterSearch = new AdapterSearch(getActivity(), commonEntityArrayList);
                        mRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity(), LinearLayout.VERTICAL,false));
                        mRecyclerView.setAdapter(adapterSearch);
                        adapterSearch.notifyDataSetChanged();
                        System.out.println("Success");
                    } else {

                        System.out.println("Fail");
                    }

            }

            @Override
            public void onFailure(Call<CommonEntity> call, Throwable t) {
                System.out.println("Fail22");
            }
        });
    }

}


//fragment_search xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.rajeev.testfragmentsotp.Fragments.FragmentSearch">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:background="@color/SecondaryColor">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:layout_marginTop="@dimen/DP_6"
            android:layout_marginBottom="@dimen/DP_6"
            android:layout_marginRight="@dimen/DP_12"
            android:layout_marginLeft="@dimen/DP_12"
           android:background="@drawable/curve_corner_white">

            <EditText
                android:id="@+id/chooseLocationSearch"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="@dimen/DP_8"
                android:drawableLeft="@drawable/ic_action_name"
                android:hint="Search Location"
                android:inputType="text"
                android:background="@null"/>
        </LinearLayout>
</LinearLayout>

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>

//AdapterSearch


import java.util.ArrayList;

/**
 * Created by CHANDAN on 10/17/2018.
 */

public class AdapterSearch extends RecyclerView.Adapter<AdapterSearch.MyHolder> {
    Context context;
    ArrayList<ArrayCommonEntity> commonEntityArrayList;

    public AdapterSearch(FragmentActivity activity, ArrayList<ArrayCommonEntity> commonEntityArrayList) {
        this.context = activity;
        this.commonEntityArrayList = commonEntityArrayList;
    }

    @Override
    public MyHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        LayoutInflater layoutInflater = (LayoutInflater)context.getSystemService(context.LAYOUT_INFLATER_SERVICE);

        View view = layoutInflater.inflate(R.layout.row_search, parent ,false);
        return new AdapterSearch.MyHolder(view);
    }

    @Override
    public void onBindViewHolder(MyHolder holder, int position) {
        holder.txtSearch.setText(commonEntityArrayList.get(position).getName());
    }

    @Override
    public int getItemCount() {
        return commonEntityArrayList.size();
    }

    class MyHolder extends RecyclerView.ViewHolder {
        TextView txtSearch;
        public MyHolder(View itemView) {
            super(itemView);
             txtSearch = itemView.findViewById(R.id.txtSearch);
        }
    }
}
