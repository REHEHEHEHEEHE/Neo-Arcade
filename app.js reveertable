import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

const supabase = createClient(
    "https://xendlckjbpqllbfzmouj.supabase.co",
    "sb_publishable_Twcn4S7CVpv-WHCz063wcg_wvQPyKkB"
);

window.register = async () => {

    const username =
        document.getElementById("regUsername").value.trim();

    const email =
        document.getElementById("regEmail").value.trim();

    const password =
        document.getElementById("regPassword").value;

    if(!username || !email || !password){
        alert("Please fill in all fields.");
        return;
    }

    const { data, error } =
        await supabase.auth.signUp({
            email,
            password
        });

    if(error){
        alert(error.message);
        return;
    }

    const userId = data.user.id;

    const { error: profileError } =
        await supabase
            .from("profiles")
            .insert({
                id:userId,
                username:username,
                email:email,
                role:"player",
                is_banned:false
            });

    if(profileError){
        alert(profileError.message);
        return;
    }

    alert("Account created successfully.");
};

window.login = async () => {

    const username =
        document.getElementById("loginUsername").value.trim();

    const password =
        document.getElementById("loginPassword").value;

    const { data, error } =
        await supabase.rpc(
            "get_email_from_username",
            {
                input_username: username
            }
        );

    if(error){
        console.error(error);
        alert("Username lookup failed.");
        return;
    }

    if(!data || data.length === 0){
        alert("Username not found.");
        return;
    }

    const email = data[0].email;

    const { error: loginError } =
        await supabase.auth.signInWithPassword({
            email,
            password
        });

    if(loginError){
        alert(loginError.message);
        return;
    }

    await loadUser();
};


window.logout = async () => {

    await supabase.auth.signOut();

    location.reload();
};
    async function loadAdminPanel(){

    const { data, error } =
        await supabase.rpc(
            "get_all_profiles"
        );

    if(error){
        console.error(error);
        return;
    }

    const tableBody =
        document.getElementById(
            "userTableBody"
        );

    tableBody.innerHTML = "";

    data.forEach(user => {

        const row =
            document.createElement("tr");

        row.innerHTML = `
            <td>${user.username}</td>
            <td>${user.role}</td>
            <td>${user.is_banned ? "Yes" : "No"}</td>

            <td>

                <button
                    onclick="changeRole(
                        '${user.id}',
                        '${
                            user.role === 'admin'
                            ? 'player'
                            : 'admin'
                        }',
                        ${user.is_banned}
                    )">

                    ${
                        user.role === 'admin'
                        ? 'Demote'
                        : 'Promote'
                    }

                </button>

                <button
                    onclick="toggleBan(
                        '${user.id}',
                        '${user.role}',
                        ${!user.is_banned}
                    )">

                    ${
                        user.is_banned
                        ? 'Unban'
                        : 'Ban'
                    }

                </button>

            </td>
        `;

        tableBody.appendChild(row);

    });

}
    window.changeRole = async (
    userId,
    newRole,
    currentBanStatus
) => {

    const { error } =
        await supabase.rpc(
            "admin_update_user",
            {
                target_user_id: userId,
                new_role: newRole,
                new_ban_status:
                    currentBanStatus
            }
        );

    if(error){
        alert(error.message);
        return;
    }

    await loadAdminPanel();
        await loadLeaderboard();
        
};
    window.toggleBan = async (
    userId,
    currentRole,
    newBanStatus
) => {

    const { error } =
        await supabase.rpc(
            "admin_update_user",
            {
                target_user_id: userId,
                new_role: currentRole,
                new_ban_status:
                    newBanStatus
                await loadAdminPanel();
await loadLeaderboard();
            }
        );

    if(error){
        alert(error.message);
        return;
    }

    await loadAdminPanel();





    async function loadAnnouncements(){

    const { data, error } =
        await supabase.rpc(
            "get_announcements"
        );

    if(error){
        console.error(error);
        return;
    }

    const container =
        document.getElementById(
            "announcementList"
        );

    container.innerHTML = "";

    data.forEach(item => {

        container.innerHTML += `
    <div class="announcement">

        <h3>${item.title}</h3>

        <p>${item.message}</p>

        <small>
            ${new Date(
                item.created_at
            ).toLocaleString()}
        </small>

    </div>
`;
                
    });

}
 window.createAnnouncement = async () => {

    const title =
        document.getElementById(
            "announcementTitle"
        ).value.trim();

    const message =
        document.getElementById(
            "announcementMessage"
        ).value.trim();

    if(!title || !message){

        alert(
            "Please fill in all fields."
        );

        return;
    }

    const { error } =
        await supabase.rpc(
            "create_announcement",
            {
                announcement_title:title,
                announcement_message:message
            }
        );

    if(error){

        alert(error.message);

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
await loadLeaderboard();
    alert(
        "Announcement posted."
    );

};

async function loadUser(){

 

    
    const { data } =
        await supabase.auth.getUser();

    const user = data.user;

    if(!user) return;

    const { data: profile, error } =
        await supabase
            .from("profiles")
            .select("*")
            .eq("id", user.id)
            .single();

    if(error || !profile){
        console.error(error);
        return;
    }
await supabase
    .from("profiles")
    .update({
        visits: profile.visits + 1
    })
    .eq("id", user.id);

profile.visits++;
    if(profile.is_banned){

        alert("This account has been banned.");

        await supabase.auth.signOut();

        location.reload();

        return;
    }

    document.getElementById("welcome").innerHTML =
        `Welcome ${profile.username}<br>Role: ${profile.role}`;

    await loadAnnouncements();

    document.getElementById("authPanel").style.display = "none";
    document.getElementById("userPanel").style.display = "block";
await loadProfileEditor(profile);
    await loadProfileEditor(profile);

    if(profile.role === "admin"){

    document.getElementById(
        "adminBadge"
    ).style.display = "block";

    document.getElementById(
        "adminPanel"
    ).style.display = "block";

    await loadAdminPanel();
        

    const { data, error } =
        await supabase
            .from("profiles")
            .select("username, visits")
            .order("visits", {
                ascending:false
            });

    if(error){
        console.error(error);
        return;
    }

    const body =
        document.getElementById(
            "leaderboardBody"
        );

    body.innerHTML = "";

    data.forEach((player,index)=>{

        body.innerHTML += `
            <tr>
                <td>#${index+1}</td>
                <td>${player.username}</td>
                <td>${player.visits}</td>
            </tr>
        `;

    });

}
}
}
async function loadLeaderboard() {

    const { data, error } = await supabase
        .from("profiles")
        .select("username, visits")
        .order("visits", { ascending: false });

    if (error) {
        console.error(error);
        return;
    }

    const body = document.getElementById("leaderboardBody");

    if (!body) return;

    body.innerHTML = "";

    data.forEach((player, index) => {

        body.innerHTML += `
            <tr>
                <td>#${index + 1}</td>
                <td>${player.username}</td>
                <td>${player.visits ?? 0}</td>
            </tr>
        `;

    });

}
window.saveProfile = async () => 

     { data:{user} } =
        await supabase.auth.getUser();

    if(!user) return;

    const file =
        document.getElementById("avatarFile").files[0];

    let avatarUrl = null;

    if(file){

        const extension =
            file.name.split(".").pop();

        const fileName =
            `${user.id}/avatar.${extension}`;

        const { error: uploadError } =
            await supabase.storage
                .from("avatars")
                .upload(
                    fileName,
                    file,
                    {
                        upsert:true
                    }
                );

        if(uploadError){

            alert(uploadError.message);

            return;

        }

        const { data } =
            supabase.storage
                .from("avatars")
                .getPublicUrl(fileName);

        avatarUrl =
            data.publicUrl;

    }

    const bio =
        document.getElementById("bio").value;

    const favourite =
        document.getElementById("favoriteGame").value;

    const updateData = {

        bio:bio,

        favourite_game:favourite

    };

    if(avatarUrl){

        updateData.avatar_url =
            avatarUrl;

    }

    const { error } =
        await supabase
            .from("profiles")
            .update(updateData)
            .eq("id",user.id);

    if(error){

        alert(error.message);

        return;

    }

    await loadUser();

    alert("Profile updated!");

};
async function loadProfileEditor(profile){

    document.getElementById("bio").value =
        profile.bio || "";

    document.getElementById("favoriteGame").value =
        profile.favourite_game || "";

    document.getElementById("avatarPreview").src =
        profile.avatar_url ||
        "https://placehold.co/150x150";

}
}
async function loadProfileEditor(profile){

    document.getElementById("bio").value =
        profile.bio || "";

    document.getElementById("favoriteGame").value =
        profile.favourite_game || "";

    document.getElementById("avatarPreview").src =
        profile.avatar_url ||
        "https://placehold.co/150x150";

}
const {
    data:{session}
} = await supabase.auth.getSession();

if(session){
    await loadUser();
}
