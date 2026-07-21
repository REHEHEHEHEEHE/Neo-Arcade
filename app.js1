import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

const supabase = createClient(
    "https://xendlckjbpqllbfzmouj.supabase.co",
    "sb_publishable_Twcn4S7CVpv-WHCz063wcg_wvQPyKkB"
);


// ===============================
// REGISTER
// ===============================

window.register = async () => {

    const username =
        document.getElementById("regUsername").value.trim();

    const email =
        document.getElementById("regEmail").value.trim();

    const password =
        document.getElementById("regPassword").value;


    if (!username || !email || !password) {

        alert("Please fill in all fields.");
        return;

    }


    const { data, error } =
        await supabase.auth.signUp({

            email,
            password

        });


    if (error) {

        alert(error.message);
        return;

    }


    if (!data.user) {

        alert(
            "Please check your email and confirm your account."
        );

        return;

    }


    const { error: profileError } =
        await supabase
            .from("profiles")
            .insert({

                id: data.user.id,
                username: username,
                email: email,
                role: "player",
                is_banned: false,
                visits: 0

            });


    if (profileError) {

        alert(profileError.message);
        return;

    }


    alert("Account created successfully.");

};



// ===============================
// LOGIN
// ===============================

window.login = async () => {


    const username =
        document
            .getElementById("loginUsername")
            .value
            .trim();


    const password =
        document
            .getElementById("loginPassword")
            .value;



    if (!username || !password) {

        alert("Please enter your username and password.");
        return;

    }



    const { data, error } =
        await supabase.rpc(
            "get_email_from_username",
            {
                input_username: username
            }
        );



    if (error) {

        console.error(error);

        alert(
            "Username lookup failed."
        );

        return;

    }



    if (!data || data.length === 0) {

        alert(
            "Username not found."
        );

        return;

    }



    const email =
        data[0].email;



    const { error: loginError } =
        await supabase.auth.signInWithPassword({

            email,
            password

        });



    if (loginError) {

        alert(loginError.message);

        return;

    }



    await loadUser();


};




// ===============================
// LOGOUT
// ===============================

window.logout = async () => {


    await supabase.auth.signOut();


    location.reload();


};




// ===============================
// LOAD CURRENT USER
// ===============================

async function loadUser() {


    const { data } =
        await supabase.auth.getUser();


    const user =
        data.user;



    if (!user) {

        return;

    }



    const { data: profile, error } =
        await supabase
            .from("profiles")
            .select("*")
            .eq("id", user.id)
            .single();



    if (error || !profile) {

        console.error(error);

        return;

    }



    // Check banned status

    if (profile.is_banned) {


        alert(
            "This account has been banned."
        );


        await supabase.auth.signOut();


        location.reload();


        return;


    }



    // Increase visits

    await supabase
        .from("profiles")
        .update({

            visits:
                (profile.visits || 0) + 1

        })
        .eq(
            "id",
            user.id
        );



    const welcome =
        document.getElementById("welcome");


    if (welcome) {


        welcome.innerHTML =
            `
            Welcome ${profile.username}
            <br>
            Role: ${profile.role}
            `;

    }



    const authPanel =
        document.getElementById("authPanel");


    const userPanel =
        document.getElementById("userPanel");



    if (authPanel) {

        authPanel.style.display =
            "none";

    }



    if (userPanel) {

        userPanel.style.display =
            "block";

    }



    await loadAnnouncements();


    await loadLeaderboard();



    if (
        typeof loadProfileEditor === "function"
    ) {

        await loadProfileEditor(profile);

    }



    if (profile.role === "admin") {


        const badge =
            document.getElementById("adminBadge");


        const panel =
            document.getElementById("adminPanel");



        if (badge) {

            badge.style.display =
                "block";

        }



        if (panel) {

            panel.style.display =
                "block";

        }



        await loadAdminPanel();


    }


}
// ===============================
// ADMIN PANEL
// ===============================

async function loadAdminPanel() {


    const { data, error } =
        await supabase.rpc(
            "get_all_profiles"
        );


    if (error) {

        console.error(error);

        return;

    }



    const tableBody =
        document.getElementById(
            "userTableBody"
        );



    if (!tableBody) {

        return;

    }



    tableBody.innerHTML = "";



    data.forEach(user => {



        const row =
            document.createElement(
                "tr"
            );



        row.innerHTML = `

        <td>
            ${user.username}
        </td>

        <td>
            ${user.role}
        </td>

        <td>
            ${
                user.is_banned
                ? "Yes"
                : "No"
            }
        </td>


        <td>


            <button
            onclick="
            changeRole(
                '${user.id}',
                '${
                    user.role === "admin"
                    ? "player"
                    : "admin"
                }',
                ${user.is_banned}
            )">

            ${
                user.role === "admin"
                ? "Demote"
                : "Promote"
            }

            </button>



            <button
            onclick="
            toggleBan(
                '${user.id}',
                '${user.role}',
                ${!user.is_banned}
            )">


            ${
                user.is_banned
                ? "Unban"
                : "Ban"
            }


            </button>


        </td>

        `;



        tableBody.appendChild(row);


    });


}





// ===============================
// CHANGE ROLE
// ===============================

window.changeRole = async (
    userId,
    newRole,
    currentBanStatus
) => {



    const { error } =
        await supabase.rpc(
            "admin_update_user",
            {

                target_user_id:
                    userId,

                new_role:
                    newRole,

                new_ban_status:
                    currentBanStatus

            }
        );



    if (error) {


        alert(
            error.message
        );


        return;


    }



    await loadAdminPanel();


    await loadLeaderboard();


};






// ===============================
// BAN / UNBAN USER
// ===============================

window.toggleBan = async (
    userId,
    currentRole,
    newBanStatus
) => {



    const { error } =
        await supabase.rpc(
            "admin_update_user",
            {

                target_user_id:
                    userId,

                new_role:
                    currentRole,

                new_ban_status:
                    newBanStatus

            }
        );



    if (error) {


        alert(
            error.message
        );


        return;


    }



    await loadAdminPanel();


    await loadLeaderboard();


};






// ===============================
// LEADERBOARD
// ===============================

async function loadLeaderboard() {



    const { data, error } =
        await supabase
            .from("profiles")
            .select(
                "username, visits"
            )
            .order(
                "visits",
                {
                    ascending:false
                }
            );



    if (error) {


        console.error(error);


        return;


    }



    const body =
        document.getElementById(
            "leaderboardBody"
        );



    if (!body) {


        return;


    }



    body.innerHTML = "";



    data.forEach(
        (
            player,
            index
        ) => {



        body.innerHTML += `

        <tr>

            <td>
                #${index + 1}
            </td>

            <td>
                ${player.username}
            </td>

            <td>
                ${player.visits || 0}
            </td>


        </tr>

        `;



    });



}
// ===============================
// ANNOUNCEMENTS
// ===============================

async function loadAnnouncements() {


    const { data, error } =
        await supabase.rpc(
            "get_announcements"
        );


    if (error) {

        console.error(error);

        return;

    }



    const container =
        document.getElementById(
            "announcementList"
        );



    if (!container) {

        return;

    }



    container.innerHTML = "";



    data.forEach(item => {


        container.innerHTML += `

        <div class="announcement">


            <h3>
                ${item.title}
            </h3>


            <p>
                ${item.message}
            </p>


            <small>

                ${
                    new Date(
                        item.created_at
                    ).toLocaleString()
                }

            </small>


        </div>

        `;


    });


}





// ===============================
// CREATE ANNOUNCEMENT
// ===============================

window.createAnnouncement = async () => {


    const title =
        document
            .getElementById(
                "announcementTitle"
            )
            .value
            .trim();



    const message =
        document
            .getElementById(
                "announcementMessage"
            )
            .value
            .trim();



    if (!title || !message) {


        alert(
            "Please fill in all fields."
        );


        return;


    }




    const { error } =
        await supabase.rpc(
            "create_announcement",
            {

                announcement_title:
                    title,

                announcement_message:
                    message

            }
        );



    if (error) {


        alert(
            error.message
        );


        console.error(error);


        return;


    }




    document.getElementById(
        "announcementTitle"
    ).value = "";



    document.getElementById(
        "announcementMessage"
    ).value = "";




    await loadAnnouncements();


    alert(
        "Announcement posted."
    );


};







// ===============================
// PROFILE EDITOR
// ===============================

async function loadProfileEditor(profile) {



    const bio =
        document.getElementById(
            "bio"
        );



    const favourite =
        document.getElementById(
            "favoriteGame"
        );



    const avatar =
        document.getElementById(
            "avatarPreview"
        );




    if (bio) {

        bio.value =
            profile.bio || "";

    }



    if (favourite) {

        favourite.value =
            profile.favourite_game || "";

    }



    if (avatar) {

        avatar.src =
            profile.avatar_url ||
            "https://placehold.co/150x150";

    }


}







// ===============================
// SAVE PROFILE
// ===============================

window.saveProfile = async () => {



    const {
        data: {
            user
        }
    } =
        await supabase.auth.getUser();



    if (!user) {

        return;

    }




    const file =
        document
            .getElementById(
                "avatarFile"
            )
            ?.files[0];



    let avatarUrl = null;




    if (file) {



        const extension =
            file.name
                .split(".")
                .pop();



        const fileName =
            `${user.id}/avatar.${extension}`;





        const { error: uploadError } =
            await supabase.storage
                .from(
                    "avatars"
                )
                .upload(
                    fileName,
                    file,
                    {
                        upsert:true
                    }
                );



        if (uploadError) {


            alert(
                uploadError.message
            );


            return;


        }





        const { data } =
            supabase.storage
                .from(
                    "avatars"
                )
                .getPublicUrl(
                    fileName
                );



        avatarUrl =
            data.publicUrl;



    }




    const updateData = {


        bio:
            document
                .getElementById(
                    "bio"
                )
                ?.value || "",



        favourite_game:
            document
                .getElementById(
                    "favoriteGame"
                )
                ?.value || ""


    };





    if (avatarUrl) {


        updateData.avatar_url =
            avatarUrl;


    }




    const { error } =
        await supabase
            .from(
                "profiles"
            )
            .update(
                updateData
            )
            .eq(
                "id",
                user.id
            );



    if (error) {


        alert(
            error.message
        );


        return;


    }




    alert(
        "Profile updated!"
    );



    await loadUser();


};







// ===============================
// AUTO LOGIN CHECK
// ===============================

const {
    data:{
        session
    }
} =
    await supabase.auth.getSession();



if (session) {


    await loadUser();


}
